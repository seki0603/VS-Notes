# 共通前提
update機能を実装するフォームもしくはボタンにidが渡っている。  
`<button wire:click="showDetail({{ $task->id }})" class="detail-button">詳細</button>`

`public function showDetail($id)`

# selectedTask方式
## livewire/component-name.blade.php
```
@if ($selectedTask)
    <div class="detail">
        <form wire:submit.prevent="update" class="detail-form" novalidate>
            <button wire:click="closeDetail" class="close" type="button">×</button>
            <table class="detail-table">
                <tr class="detail-table__row">
                    <th class="detail-table__header">タイトル</th>
                    <td class="detail-table__item">
                        <input wire:model.defer="selectedTask.title" class="detail-form__input" type="text">
                        @error('selectedTask.title')
                        <p class="error">{{ $message }}</p>
                        @enderror
                    </td>
                </tr>

                // 中略

                <tr class="detail-table__row">
                    <th class="detail-table__header">
                        {{-- 新しい画像選択 --}}
                        <div>
                            <label class="file-input__label">画像を選択
                                <input wire:model="image" class="file-input" type="file">
                            </label>
                        </div>
                        @if ($selectedTask['image_path'] && !$image)
                        <button wire:click="deleteImage" class="delete-image">画像のみ削除</button>
                        @endif
                    </th>
                    <td class="detail-table__item">
                        {{-- 更新前プレビュー --}}
                        @if ($image)
                        <p class="detail-form__title">更新前画像プレビュー</p>
                        <img class="preview" src="{{ $image->temporaryUrl() }}" alt="">
                        {{-- 現在の画像 --}}
                        @elseif ($selectedTask['image_path'])
                        <img class="detail-table__image" src="{{ asset('storage/' . $selectedTask['image_path']) }}"
                            alt="">
                        @endif
                        @error('image')
                        <p class="error">{{ $message }}</p>
                        @enderror
                    </td>
                </tr>
            </table>
            <div class="button__wrapper">
                <button class="update-button" type="submit">更新</button>
                <button wire:click="delete" class="delete-button" type="button">削除</button>
            </div>
        </form>
    </div>
    @endif
```

## ComponentName.php
```
public function update()
    {
        $request = new TaskRequest();
        $rules = $request->rules();
        $errorMessages = $request->messages();

        $taskRules = collect($rules)
            ->except('image')
            ->mapWithKeys(fn($rule, $key) => ["selectedTask.$key" => $rule])
            ->toArray();

        $taskMessages = collect($errorMessages)
            ->filter(fn($msg, $key) => !str_starts_with($key, 'image.'))
            ->mapWithKeys(fn($msg, $key) => ["selectedTask.$key" => $msg])
            ->toArray();

        if ($this->image) {
            $taskRules['image'] = $rules['image'];

            $imageMessages = collect($errorMessages)
                ->filter(fn($msg, $key) => str_starts_with($key, 'image.'))
                ->toArray();

            $taskMessages = array_merge($taskMessages, $imageMessages);
        }

        $validated = $this->validate($taskRules, $taskMessages);

        $task = Task::find($this->selectedTask['id']);

        if ($this->image) {
            if ($task->image_path && Storage::disk('public')->exists($task->image_path)) {
                Storage::disk('public')->delete($task->image_path);
            }

            $path = $this->image->store('tasks', 'public');
            $validated['selectedTask']['image_path'] = $path;
        }

        $task->update($validated['selectedTask']);

        $this->reset(['image']);
        $this->imageName = null;
        $this->closeDetail();
    }
```

## 処理順序
* 画像がある場合は配列化から除く...`except('image')`
* 配列化するmodelは`selectedTask.title`の形にする
* 画像はキーで配列と別処理にし、最後配列にmergeする
* 画像が更新される場合、旧画像をStorageから削除


# form配列方式
## livewire/component-name.blade.php
```
@if ($showModal)
    <div class="detail">
        <form wire:submit.prevent="update" class="detail-form" novalidate>
            <button wire:click="closeDetail" class="close" type="button">×</button>
            <table class="detail-table">
                <tr class="detail-table__row">
                    <th class="detail-table__header">タイトル</th>
                    <td class="detail-table__item">
                        <input wire:model="form.title" class="detail-form__input" type="text">
                    </td>
                    @error('form.title')
                    <td class="error">{{ $message }}</td>
                    @enderror
                </tr>

                // 中略

                <tr class="detail-table__row">
                    <th class="detail-table__header">
                        {{-- 新しい画像選択 --}}
                        <div>
                            <label class="file-input__label">画像を選択
                                <input wire:model="image" class="file-input" type="file">
                            </label>
                        </div>
                        @if ($displayImagePath && !$image)
                        <button wire:click="deleteImage" class="delete-image">画像のみ削除</button>
                        @endif
                    </th>
                    <td class="detail-table__item">
                        {{-- 更新前プレビュー --}}
                        @if ($image)
                        <p class="detail-form__title">更新前画像プレビュー</p>
                        <img class="preview" src="{{ $image->temporaryUrl() }}" alt="">
                        {{-- 現在の画像 --}}
                        @elseif ($displayImagePath)
                        <img class="detail-table__image" src="{{ asset('storage/' . $displayImagePath) }}"
                            alt="">
                        @endif
                    </td>
                    @error('image')
                    <td class="error">{{ $message }}</td>
                    @enderror
                </tr>
            </table>
            <div class="button__wrapper">
                <button class="update-button" type="submit">更新</button>
                <button wire:click="delete" class="delete-button" type="button">削除</button>
            </div>
        </form>
    </div>
    @endif
```

## ComponentName.php
### 配列化を定義
```
public $form = [
        'title' => '',
        'description' => '',
        'due_date' => '',
        'status' => 'doing',
    ];
```

### formRequestをLivewre用にキー変換
```
 private function rulesForLivewire()
    {
        $rules = (new TaskRequest())->rules();

        $mapped = [];
        foreach ($rules as $key => $value) {
            if ($key === 'image') {
                $mapped['image'] = $value;
            } else {
                $mapped["form.$key"] = $value;
            }
        }
        return $mapped;
    }

    private function messagesForLivewire()
    {
        $messages = (new TaskRequest())->messages();

        $mapped = [];
        foreach ($messages as $key => $value) {
            if (str_starts_with($key, 'image')) {
                $mapped[$key] = $value;
            } else {
                $mapped["form.$key"] = $value;
            }
        }
        return $mapped;
    }
```

### 配列のキーを指定
```
public function showDetail($id)
    {
        $this->task = Task::find($id);

        $this->form = [
            'title' => $this->task->title,
            'description' => $this->task->description,
            'due_date' => $this->task->due_date,
            'status' => $this->task->status,
        ];
        $this->displayImagePath = $this->task->image_path;

        $this->showModal = true;
    }
```
* 上記はモーダル（詳細）表示の時点でキーを指定している

### update処理記述
```
public function update()
    {
        $validated = $this->validate(
            $this->rulesForLivewire(),
            $this->messagesForLivewire()
        );

        if ($this->image) {
            if ($this->task->image_path && Storage::disk('public')->exists($this->task->image_path)) {
                Storage::disk('public')->delete($this->task->image_path);
            }
            $validated['image'] = $this->image->store('tasks', 'public');
        } else {
            $validated['image'] = $this->task->image_path;
        }

        $this->task->update([
            'title' => $this->form['title'],
            'description' => $this->form['description'],
            'due_date' => $this->form['due_date'],
            'status' => $this->form['status'],
            'image_path' => $validated['image'],
        ]);

        $this->resetForm();
        $this->showModal = false;
    }
```
* キー変換したバリデーションを関数名で使用
* 画像は別処理
* 更新する値は`配列名['キー名']`
