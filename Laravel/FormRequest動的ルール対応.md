```
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class CorrectionRequest extends FormRequest
{
    /**
     * ユーザーがこのリクエストを実行できるかどうか
     */
    public function authorize()
    {
        return true;
    }

    /**
     * 固定バリデーションルール
     */
    public function rules()
    {
        return [
            'clock_in'  => ['required', 'date_format:H:i'],
            'clock_out' => ['required', 'date_format:H:i', 'after:clock_in'],
            'note'      => ['required'],
        ];
    }

    /**
     * カスタムエラーメッセージ
     */
    public function messages()
    {
        return [
            // 出退勤
            'clock_in.required'  => '出勤時間を入力してください',
            'clock_out.required' => '退勤時間を入力してください',
            'clock_out.after'    => '出勤時間もしくは退勤時間が不適切な値です',

            // 休憩（動的キー共通）
            '*.date_format' => '休憩時間が不適切な値です',
            '*.after'       => '休憩時間が不適切な値です',
            '*.before'      => '休憩時間もしくは退勤時間が不適切な値です',

            // 備考
            'note.required' => '備考を記入してください',
        ];
    }

    /**
     * 動的に追加された休憩行にルールを適用する
     */
    public function validator($factory)
    {
        // まず基本ルールでバリデータを生成
        $validator = $factory->make(
            $this->validationData(),
            $this->rules(),
            $this->messages()
        );

        // 🔍 break_start_1, break_end_1 ... を自動検出してルール追加
        foreach ($this->all() as $key => $value) {
            if (preg_match('/^break_start_\d+$/', $key)) {
                $num = (int) str_replace('break_start_', '', $key);

                $validator->addRules([
                    "break_start_{$num}" => [
                        'nullable',
                        'date_format:H:i',
                        'after:clock_in',
                        'before:clock_out',
                    ],
                    "break_end_{$num}" => [
                        'nullable',
                        'date_format:H:i',
                        'after:break_start_' . $num,
                        'before:clock_out',
                    ],
                ]);
            }
        }

        return $validator;
    }
}
```