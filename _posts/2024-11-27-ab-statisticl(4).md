---
layout: post
title:  "[AB Test] Delta, Delta %"
date:   2024-11-27
last_modified_at: 2024-11-27
categories: [AB Test, Statistic]
tags: [Statistic, Data]
---

{: .box-success}
주로 분석할 때 많이 사용하는 이론을 간략히 정리합니다.

# **분산 추정 및 민감도**

실험에서의 **정확한 분산 추정**은 신뢰할 수 있는 결과를 얻고 올바른 결론을 내리는 데 필수적입니다. 분산을 잘못 추정하면 p-값과 신뢰 구간이 부정확해져, 가설 검정의 결과를 잘못 해석할 수 있습니다.  
특히, 분산을 과대 추정하면 **거짓 부정(false negative)**이 발생하고, 과소 추정하면 **거짓 긍정(false positive)**이 발생할 수 있습니다.

---

## **일반적인 함정**

### **1. Δ% (델타 퍼센트) 사용 시의 문제점**

실험 결과를 보고할 때, 절대 차이(Δ) 대신 상대적인 차이인 **Δ%**를 사용하는 경우가 많습니다.  
이는 결과의 상대적 영향을 이해하는 데 도움이 되지만, Δ%의 분산을 추정할 때 주의가 필요합니다.

**잘못된 분산 추정 방법:**

많은 사람들이 Δ%의 분산을 다음과 같이 잘못 추정합니다:

$$
\text{Var}(\Delta\%) = \frac{\text{Var}(\Delta)}{(\bar{Y}_C)^2}
$$

여기서 $$\bar{Y}_C$$는 대조군의 평균입니다.  
그러나 이 방법은 $$\bar{Y}_C$$ 자체가 **확률 변수**라는 사실을 무시한 것입니다.  
$$\bar{Y}_C$$의 변동성을 고려하지 않으면 분산이 과소 추정되어 결과의 신뢰성이 떨어집니다.

**올바른 분산 추정 방법:**

Δ%의 분산을 정확하게 추정하기 위해서는 $$\Delta$$와 $$\bar{Y}_C$$ 모두의 변동성을 고려해야 합니다.  
이를 위해 **델타 방법(Delta Method)**을 사용하여 다음과 같이 분산을 추정할 수 있습니다:

$$
\text{Var}\left( \Delta\% \right) = \text{Var}\left( \frac{\Delta}{\bar{Y}_C} \times 100\% \right) = \left( \frac{100\%}{\bar{Y}_C} \right)^2 \left( \text{Var}(\Delta) + \left( \frac{\Delta^2}{\bar{Y}_C^2} \right) \text{Var}(\bar{Y}_C) - 2 \frac{\Delta}{\bar{Y}_C} \text{Cov}(\Delta, \bar{Y}_C) \right)
$$

이 공식은 $$\Delta$$와 $$\bar{Y}_C$$의 **분산**뿐만 아니라 **공분산**까지 고려합니다.

**공분산 고려:**

- 대조군과 실험군이 **독립적**인 경우, 공분산 $$\text{Cov}(\Delta, \bar{Y}_C)$$는 0이 되며, 공식이 단순화됩니다.
- 그러나 두 그룹이 **관련성**이 있는 경우, 공분산을 무시하면 잘못된 분산 추정으로 이어질 수 있습니다.

---

## **분산 추정을 위한 방법**

### **1. 델타 방법 (Delta Method)**

델타 방법은 확률 변수의 함수에 대한 분산을 근사하기 위한 통계 기법입니다. 충분히 큰 표본 크기를 가진 경우에 효과적입니다. Δ%의 분산을 추정할 때, 델타 방법을 적용하여 위의 공식을 유도할 수 있습니다.

### **2. 부트스트랩 방법 (Bootstrap Method)**

통계적 가정이 충족되지 않거나 복잡한 지표의 분산을 추정해야 하는 경우, **부트스트랩** 방법을 사용할 수 있습니다. 부트스트랩은 표본 데이터에서 **재표본 추출**을 반복하여 분산을 추정하는 비모수적 방법입니다. 이는 계산 비용이 많이 들 수 있지만, 다양한 상황에 적용할 수 있는 강력한 기술입니다.


### **Δ%의 분산 추정에 대한 이해**

실험에서 대조군과 실험군의 평균 결과를 비교할 때, 평균의 차이(Δ)를 대조군 평균의 백분율(Δ%)로 나타내는 경우가 많습니다.  
이는 처리 효과에 대한 상대적인 변화를 이해하는 데 도움이 됩니다. 그러나 Δ%의 분산(또는 표준 오차)을 추정할 때, 분자와 분모 모두의 변동성을 정확하게 고려하는 것이 중요합니다.

---

### **단계별 계산 예시**

**가정된 데이터:**

- **대조군 평균 ($$\bar{Y}_C$$)**: 50
- **실험군 평균 ($$\bar{Y}_T$$)**: 55
- **평균의 차이 ($$\Delta$$)**: $$\bar{Y}_T - \bar{Y}_C = 5$$
- **대조군 표준편차 ($$s_C$$)**: 10
- **실험군 표준편차 ($$s_T$$)**: 12
- **표본 크기 ($$n_C = n_T$$)**: 100

**1. 평균의 분산 계산:**

대조군 평균의 분산:

$$
\text{Var}(\bar{Y}_C) = \frac{s_C^2}{n_C} = \frac{10^2}{100} = 1
$$

실험군 평균의 분산:

$$
\text{Var}(\bar{Y}_T) = \frac{s_T^2}{n_T} = \frac{12^2}{100} = 1.44
$$

**2. Δ의 분산 계산:**

$$
\text{Var}(\Delta) = \text{Var}(\bar{Y}_T) + \text{Var}(\bar{Y}_C) = 1.44 + 1 = 2.44
$$

**3. Δ와 $$\bar{Y}_C$$의 공분산 계산:**

대조군과 실험군이 독립적인 경우, 공분산은 0입니다:

$$
\text{Cov}(\Delta, \bar{Y}_C) = \text{Cov}(\bar{Y}_T - \bar{Y}_C, \bar{Y}_C) = \text{Cov}(\bar{Y}_T, \bar{Y}_C) - \text{Var}(\bar{Y}_C) = 0 - 1 = -1
$$

그러나 두 그룹이 완전히 독립적이라면 $$\text{Cov}(\bar{Y}_T, \bar{Y}_C) = 0$$이므로 공분산은 0입니다.

**4. 델타 방법 공식 적용:**

공분산이 0이므로 공식은 다음과 같이 단순화됩니다:

$$
\text{Var}\left( \frac{\Delta}{\bar{Y}_C} \right) \approx \frac{1}{(\bar{Y}_C)^2} \left( \text{Var}(\Delta) + \left( \frac{\Delta^2}{\bar{Y}_C^2} \right) \text{Var}(\bar{Y}_C) \right)
$$

수치를 대입하면:

$$
\text{Var}\left( \frac{5}{50} \right) \approx \frac{1}{50^2} \left( 2.44 + \left( \frac{5^2}{50^2} \right) \times 1 \right)
$$

계산 단계:

- $$(\bar{Y}_C)^2 = 50^2 = 2500$$
- $$\frac{\Delta^2}{\bar{Y}_C^2} = \left( \frac{5}{50} \right)^2 = \left( \frac{1}{10} \right)^2 = 0.01$$

따라서:

$$
\text{Var}\left( \frac{\Delta}{\bar{Y}_C} \right) \approx \frac{1}{2500} \left( 2.44 + 0.01 \times 1 \right) = \frac{1}{2500} (2.44 + 0.01) = \frac{1}{2500} (2.45) = \frac{2.45}{2500} = 0.00098
$$

**5. 표준 오차 계산:**

$$
\text{SE}\left( \frac{\Delta}{\bar{Y}_C} \right) = \sqrt{\text{Var}\left( \frac{\Delta}{\bar{Y}_C} \right)} = \sqrt{0.00098} \approx 0.0313
$$

**6. Δ%와 신뢰 구간 계산:**

Δ%는:

$$
\Delta\% = \frac{\Delta}{\bar{Y}_C} \times 100\% = \frac{5}{50} \times 100\% = 10\%
$$

95% 신뢰 구간은:

$$
\Delta\% \pm Z \times \text{SE}\left( \frac{\Delta}{\bar{Y}_C} \times 100\% \right)
$$

여기서 $$Z = 1.96$$ (95% 수준에서의 표준 정규 분포 값)

따라서 표준 오차를 백분율로 변환하면:

$$
\text{SE}(\Delta\%) = 0.0313 \times 100\% = 3.13\%
$$

신뢰 구간은:

$$
10\% \pm 1.96 \times 3.13\% = 10\% \pm 6.14\%
$$

즉, 95% 신뢰 구간은 약 **3.86%에서 16.14%** 사이입니다.

---

### **해석 및 중요성**

올바른 분산 추정을 통해 대조군 평균의 변동성을 고려하여 Δ%의 신뢰 구간을 정확하게 계산할 수 있습니다. 이는 결과의 신뢰성을 높이고, 잘못된 결론을 내릴 위험을 줄여줍니다.
만약 대조군 평균의 변동성을 무시했다면, 분산이 과소 추정되어 신뢰 구간이 좁아지고, 결과가 통계적으로 유의미하다고 잘못 판단할 수 있습니다.

---

### **Δ%만 사용할 때 발생할 수 있는 문제점**

1. **분모가 작을 때의 불안정성**: 대조군 평균이 매우 작거나 0에 가까우면, Δ%는 극단적으로 커지거나 정의되지 않을 수 있습니다.
2. **해석의 어려움**: 백분율 변화는 측정 척도의 하한(예: 음수가 될 수 없음)이나 데이터가 비대칭인 경우 오해를 불러일으킬 수 있습니다.
3. **비교의 어려움**: 다른 연구나 그룹 간에 대조군 평균이 다를 경우, Δ%는 직접 비교하기 어려울 수 있습니다.
4. **비율 특성**: 비율 자체가 분산을 크게 만들 수 있으며, 특히 분모의 변동성이 큰 경우 더욱 그렇습니다.

---

### **추가 권장 사항**

- **원시 차이 보고**: Δ%뿐만 아니라 평균의 절대 차이(Δ)도 함께 보고하여 결과에 대한 완전한 정보를 제공합니다.
- **대체 효과 크기 사용**: 상황에 따라 Cohen의 d, 상대 위험도 등의 다른 효과 크기 지표 사용을 고려합니다.
- **견고한 방법 적용**: 데이터가 통계적 가정을 충족하지 않는 경우, 비모수적 방법이나 부트스트랩을 사용하여 분산과 신뢰 구간을 추정합니다.
- **가정 검토**: 통계적 방법의 가정(정규성, 독립성 등)이 만족되는지 확인합니다.

---


```R
# 주어진 데이터 설정
n_C <- 100         # 대조군 표본 크기
n_T <- 100         # 실험군 표본 크기
s_C <- 10          # 대조군 표준편차
s_T <- 12          # 실험군 표준편차
mean_C <- 50       # 대조군 평균
mean_T <- 55       # 실험군 평균

# 평균의 차이와 Δ% 계산
delta <- mean_T - mean_C
delta_percent <- (delta / mean_C) * 100

# 평균의 분산 계산
var_mean_C <- (s_C^2) / n_C
var_mean_T <- (s_T^2) / n_T

# Δ의 분산 계산
var_delta <- var_mean_T + var_mean_C

# Δ%의 분산 계산 (델타 방법 적용)
var_delta_percent <- (1 / mean_C^2) * (var_delta + (delta^2 / mean_C^2) * var_mean_C) * 10000  # 백분율로 변환하기 위해 100^2 곱함

# Δ%의 표준 오차 계산
se_delta_percent <- sqrt(var_delta_percent)

# 95% 신뢰 구간 계산
z <- 1.96  # 95% 신뢰 수준의 z-값
ci_lower <- delta_percent - z * se_delta_percent
ci_upper <- delta_percent + z * se_delta_percent

# 결과 출력
cat(sprintf("Δ%% (백분율 차이): %.2f%%\n", delta_percent))
cat(sprintf("표준 오차: %.2f%%\n", se_delta_percent))
cat(sprintf("95%% 신뢰 구간: [%.2f%% , %.2f%%]\n", ci_lower, ci_upper))
```

**실행 결과:**

```
Δ% (백분율 차이): 10.00%
표준 오차: 3.13%
95% 신뢰 구간: [3.86% , 16.14%]

```

---

**참고 문헌**

- **Trustworthy Online Controlled Experiments**