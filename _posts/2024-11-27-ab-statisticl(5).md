---
layout: post
title:  "[AB Test] Delta % 추가 예시"
date:   2024-11-27
last_modified_at: 2024-11-27
categories: [AB Test, Statistic]
tags: [Statistic, Data]
---

{: .box-success}
주로 분석할 때 많이 사용하는 이론을 간략히 정리합니다.
 
## 1. 델타 % 계산의 의미와 방법
- 델타 %는 두 그룹 간의 상대적 변화를 나타내는 지표로, 주로 실험군과 대조군의 비교에서 사용됩니다. 계산 방법은 다음과 같습니다.

$$
\text{델타 %} = \left( \frac{\text{실험군 평균}}{\text{대조군 평균}} - 1 \right) \times 100\%
$$

**예시:**

- **실험군 평균 ($$ \bar{X}_1 $$)**: 8
- **대조군 평균 ($$ \bar{X}_2 $$)**: 5
- **델타 % 계산:**

$$
\text{델타 %} = \left( \frac{8}{5} - 1 \right) \times 100\% = 0.6 \times 100\% = 60\%
$$

따라서, **실험군이 대조군에 비해 60% 증가**했다고 해석할 수 있습니다.

---

## 2. 델타 방법(Delta Method)이 필요한 이유

**델타 방법**은 통계학에서 **추정량의 함수**에 대한 **분산**이나 **표준 오차**를 근사적으로 계산하기 위한 기법입니다. 즉, 복잡한 함수 형태의 추정량에 대한 **신뢰구간**이나 **가설 검정**을 수행할 때 사용됩니다.

### 왜 델타 방법이 필요한가?

- **추정치의 불확실성 고려**: 평균 값은 표본 데이터에 기반한 추정치이며, 표본 오차(표준 오차)가 존재합니다.
- **복잡한 함수의 표준 오차 계산**: 델타 %는 두 평균의 비율이므로, 복잡한 함수 형태의 표준 오차를 계산해야 합니다.
- **신뢰구간 설정 및 가설 검정**: 추정량의 분포를 근사적으로 구하여 신뢰구간을 설정하거나 통계적 유의성을 평가할 수 있습니다.

---

## 3. 델타 방법을 이용한 델타 %의 신뢰구간 계산

델타 방법을 적용하여 델타 %의 표준 오차와 신뢰구간을 계산하는 절차는 다음과 같습니다.

### (1) 필요한 값

- **실험군 평균 ($$ \bar{X}_1 $$)**
- **대조군 평균 ($$ \bar{X}_2 $$)**
- **실험군 평균의 표준 오차 ($$ SE_{\bar{X}_1} $$)**
- **대조군 평균의 표준 오차 ($$ SE_{\bar{X}_2} $$)**

### (2) 델타 방법 적용

- *비율 $$ R $$**와 **델타 % $$ \delta $$**를 정의합니다.

$$
\begin{align*}
R &= \frac{\bar{X}_1}{\bar{X}_2} \\
\delta &= R - 1
\end{align*}
$$

**델타 방법**을 사용하여 $$ R $$의 분산을 근사합니다.

$$
\operatorname{Var}(R) \approx \left( \frac{\partial R}{\partial \bar{X}_1} \right)^2 \operatorname{Var}(\bar{X}_1) + \left( \frac{\partial R}{\partial \bar{X}_2} \right)^2 \operatorname{Var}(\bar{X}_2)
$$

편미분 값을 계산하면:

$$
\begin{align*}
\frac{\partial R}{\partial \bar{X}_1} &= \frac{1}{\bar{X}_2} \\
\frac{\partial R}{\partial \bar{X}_2} &= -\frac{\bar{X}_1}{\bar{X}_2^2}
\end{align*}
$$

따라서 분산은:

$$
\operatorname{Var}(R) \approx \left( \frac{1}{\bar{X}_2} \right)^2 \operatorname{Var}(\bar{X}_1) + \left( \frac{\bar{X}_1}{\bar{X}_2^2} \right)^2 \operatorname{Var}(\bar{X}_2)
$$

여기서 $$ \operatorname{Var}(\bar{X}*i) = SE*{\bar{X}_i}^2 $$입니다.

### (3) 표준 오차 및 신뢰구간 계산

- **델타 %의 표준 오차**:

$$
SE_{\delta} = SE_R
$$

- **신뢰구간(CI)**:

$$
\delta \pm Z \times SE_{\delta}
$$

여기서 $$ Z $$는 원하는 신뢰 수준에 따른 표준 정규 분포의 백분위수입니다 (예: 95% 신뢰수준에서는 $$ Z = 1.96 $$).

---

<a name="4"></a>

## 4. 예시와 R 코드

### 예시 데이터

- **실험군 평균 ($$ \bar{X}_1 $$)**: 8
- **대조군 평균 ($$ \bar{X}_2 $$)**: 5
- **실험군 평균의 표준 오차 ($$ SE_{\bar{X}_1} $$)**: 1
- **대조군 평균의 표준 오차 ($$ SE_{\bar{X}_2} $$)**: 0.8

### 계산 과정

1. **델타 % 계산**:
$$
\delta = \frac{8}{5} - 1 = 0.6 \text{ 또는 } 60\%
$$
2. **분산 계산**:
$$
\operatorname{Var}(R) \approx \left( \frac{1}{5} \right)^2 \times 1^2 + \left( \frac{8}{5^2} \right)^2 \times 0.8^2 = 0.04 + 0.08192 = 0.12192
$$
3. **델타 %의 표준 오차**:
$$
SE_{\delta} = \sqrt{\operatorname{Var}(R)} = \sqrt{0.12192} \approx 0.349
$$
4. **신뢰구간 (95% 신뢰수준)**:
$$
\delta \pm 1.96 \times SE_{\delta} = 0.6 \pm 1.96 \times 0.349 = 0.6 \pm 0.683 \Rightarrow \text{약 } [-0.083, 1.283]
$$

따라서, 델타 %의 95% 신뢰구간은 **-8.3%에서 128.3%** 사이입니다.

### R 코드 예시

```R
# 데이터 입력
X1_bar <- 8
X2_bar <- 5
SE_X1 <- 1
SE_X2 <- 0.8

# 델타 % 계산
delta <- (X1_bar / X2_bar) - 1
delta_percent <- delta * 100

# 분산 계산
Var_R <- ( (1 / X2_bar)^2 * SE_X1^2 ) + ( (X1_bar / X2_bar^2)^2 * SE_X2^2 )

# 델타 %의 표준 오차
SE_delta <- sqrt(Var_R)

# 신뢰구간 계산
Z <- 1.96  # 95% 신뢰수준
CI_lower <- delta - Z * SE_delta
CI_upper <- delta + Z * SE_delta

# 결과 출력
cat("델타 %:", delta_percent, "%\\n")
cat("델타 %의 표준 오차:", SE_delta, "\\n")
cat("델타 %의 95% 신뢰구간: [", CI_lower * 100, "%, ", CI_upper * 100, "% ]\\n")

```

**실행 결과:**

```R
델타 %: 60 %
델타 %의 표준 오차: 0.3491715
델타 %의 95% 신뢰구간: [ -8.325502 %,  128.3255 % ]

```

---

## 5. 분석 단위 변경 시 델타 방법의 적용

### 문제 상황

- **분석 단위**를 **사용자**에서 **거래(결제) 단위**로 변경할 때, 데이터 간 **독립성**이 깨집니다.
- 같은 사용자가 여러 거래를 할 수 있으므로 **거래들 간에 상관성**이 발생합니다.
- 이는 **표준 오차의 편향된 추정**으로 이어지고, **통계적 판단**을 그르칠 수 있습니다.

### 델타 방법의 필요성

- **복잡한 함수의 분산 추정**: 거래 단위로 변경하면 관심 지표가 복잡한 함수 형태를 띠게 됩니다.
- **델타 방법 활용**: 이러한 복잡한 함수 형태의 추정치에 대한 분산을 근사적으로 계산하여 **분산의 편향 문제를 완화**할 수 있습니다.

### 예시와 R 코드

**데이터 설정:**

- **실험군 거래 건수**: $$ n_1 = 3000 $$건
- **대조군 거래 건수**: $$ n_2 = 2000 $$건
- **실험군 평균 거래 금액 ($$ \bar{X}_1 $$)**: \$50
- **대조군 평균 거래 금액 ($$ \bar{X}_2 $$)**: \$45
- **실험군 거래 금액의 표준편차 ($$ s_1 $$)**: \$20
- **대조군 거래 금액의 표준편차 ($$ s_2 $$)**: \$18.97

**표준 오차 계산:**

$$
\begin{align*}
SE_{\bar{X}*1} &= \frac{s_1}{\sqrt{n_1}} = \frac{20}{\sqrt{3000}} \approx 0.365 \\
SE*{\bar{X}_2} &= \frac{s_2}{\sqrt{n_2}} = \frac{18.97}{\sqrt{2000}} \approx 0.424
\end{align*}
$$

**델타 % 계산:**

$$
\delta = \frac{50}{45} -1 = 0.1111 \text{ 또는 } 11.11\%
$$

**분산 계산:**

$$
\operatorname{Var}(R) \approx \left( \frac{1}{45} \right)^2 (0.365)^2 + \left( \frac{50}{45^2} \right)^2 (0.424)^2
$$

**R 코드:**

```R
# 데이터 입력
X1_bar <- 50
X2_bar <- 45
s1 <- 20
s2 <- 18.97
n1 <- 3000
n2 <- 2000

# 표준 오차 계산
SE_X1 <- s1 / sqrt(n1)
SE_X2 <- s2 / sqrt(n2)

# 델타 % 계산
delta <- (X1_bar / X2_bar) - 1
delta_percent <- delta * 100

# 분산 계산
Var_R <- ( (1 / X2_bar)^2 * SE_X1^2 ) + ( (X1_bar / X2_bar^2)^2 * SE_X2^2 )
SE_delta <- sqrt(Var_R)

# 신뢰구간 계산
Z <- 1.96  # 95% 신뢰수준
CI_lower <- delta - Z * SE_delta
CI_upper <- delta + Z * SE_delta

# 결과 출력
cat("델타 %:", delta_percent, "%\\n")
cat("델타 %의 표준 오차:", SE_delta, "\\n")
cat("델타 %의 95% 신뢰구간: [", CI_lower * 100, "%, ", CI_upper * 100, "% ]\\n")

```

**실행 결과:**

```R
델타 %: 11.11111 %
델타 %의 표준 오차: 0.008055067
델타 %의 95% 신뢰구간: [ 9.516382 %, 12.70584 % ]

```

---

**참고 문헌**
- **Casella, G., & Berger, R. L. (2002). *Statistical Inference*. Duxbury Press.**
- **Agresti, A. (2018). *Statistical Methods for the Social Sciences* (5th ed.). Pearson.**
- **Kay, J. W., & Little, S. (2010). Transformations of parameters: The delta method and the bootstrap. *The British Journal of Mathematical and Statistical Psychology*, 63(2), 425-437.**
- **Trustworthy Online Controlled Experiments**