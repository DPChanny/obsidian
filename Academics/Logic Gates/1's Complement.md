1의 보수법은 2개의 0이 존재할 수 있는 가능성이 있어 이 오차를 보정하기 위해 뺼셈을 하는 과정에 있어 복잡성이 있다.

## A - B with 1's complement
### In case of A > B
$$ 
A - B \rightarrow A + !B = (2^n) + (A - B) \xrightarrow{\text{Has end carry}} (A - B) + 1
$$
1의 보수 법에서는 0000, 1000 과 같이 0을 표현하는 방법이 2개다. 따라서 아래와 같이 정의하기에 보정 과정이 필요함.
### In case of A < B
$$
A - B \xrightarrow{\text{Does not have end carry}} A + !B = B - A
$$