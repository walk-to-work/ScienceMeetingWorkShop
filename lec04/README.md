# 演習4 ADC

今回はピンからの入力電圧を連続値(アナログ)で受け取るためのペリフェラル，Analog Digital Converter:ADCを用いて，Nucleoで取得したセンサの値をPCのターミナル上で表示する．

## CubeMXでの設定

### ADCの設定

今回仕様する基板ではA0ピン，つまりPA0ピンに温度センサが接続されている．
左のタブから

[Analog]>[ADC1]を選択し，[IN1]を[IN1 Single-ended]

と選択することで有効化することができる．

![ADCピン](./img/adc_pin_assign.png)

また，設定で[Continuous Conversion]を[Enable]にすること(忘れると一度値を取ったまま，次ADC_Startが実行されるまで動かない)

![important](./img/important.png)

最終的なピン割り当てはこの通り

![ピン割り当て](./img/pin_assign.png)

確認できたら[GENERATE CODE]する．

## SW4STM32でのコーディング


ADCの値の取得はGPIOのようにワンラインではできない．
```HAL_ADCEx_Calibration_Start```でキャリブレーション，```HAL_ADC_Start```でADCのスタートを行い，
```HAL_ADC_PollForConversion```で変換終了を確認してから```HAL_ADC_GetValue```で値を受け取らなければ，適切な値を受け取れない．

次のコードはHALハンドルが```hadc1```のADCチャンネルでの値取得の例である．

```c
HAL_ADCEx_Calibration_Start(&hadc1, ADC_SINGLE_ENDED);
HAL_ADC_Start(&hadc1);
int adc1_val = 0;
while(1){
    if( HAL_ADC_PollForConversion(&hadc1 , 10 ) )
	    adc1_val = HAL_ADC_GetValue(&hadc1);
}
HAL_ADC_Stop(&hadc1);
```

演習4の解説は以上です．

課題4
- 1秒ごとに温度センサの値を取得し，ターミナルに表示してください
- PA5に接続された照度センサも同様に有効化し，温度・照度センサの値を1秒ごとにターミナルに表示してください．

[実装例はこちら](./main.c)
