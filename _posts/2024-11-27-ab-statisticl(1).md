---
layout: post
title:  "[AB Test] 가장 일반적인 검정 - t검정"
date:   2024-11-27
last_modified_at: 2024-11-27
categories: [AB Test, Statistic]
tags: [Statistic, Data]
---

{: .box-success}
주로 분석할 때 많이 사용하는 이론을 간략히 정리합니다.


# **두 표본 t-검정 (Two-Sample t-Test)**

두 표본 t-검정은 두 개의 **독립적인** 집단의 평균 차이가 통계적으로 유의한지를 판단하기 위한 통계적 방법입니다.   
이는 온라인 실험에서 처리군과 대조군 간의 효과를 비교할 때 자주 사용됩니다.

---

## **1. 가정사항 (Assumptions)**

두 표본 t-검정을 적용하기 위해서는 다음과 같은 가정이 필요합니다:
1. **독립성 (Independence)**: 각 표본은 서로 독립적이며, 표본 내의 관측치들도 독립적이어야 합니다.
2. **정규성 (Normality)**: 각 집단의 데이터는 정규분포를 따릅니다.
3. **등분산성 (Homogeneity of Variance)**: 두 집단의 분산이 동일합니다.

{: .box-success}
**주의사항**: 이러한 가정들이 만족되지 않으면 검정 결과가 신뢰할 수 없게 됩니다. 특히 **독립성**은 추정량의 **불편성 (Unbiasedness)**을 보장하는 중요한 조건입니다.

---

## **2. 검정 통계량 (Test Statistic)**

두 집단의 평균을 $$X_1$$, $$X_2$$, 표준편차를 $$S_1$$, $$S_2$$, 표본 크기를 $$n_1$$, $$n_2$$라고 하면, **등분산을 가정한** 두 표본 t-검정의 검정 통계량 t는 다음과 같습니다:

$$
t = \frac{\bar{X}_1 - \bar{X}_2}{S_p \sqrt{\dfrac{1}{n_1} + \dfrac{1}{n_2}}}
$$

여기서 $$S_p$$는 **풀링된 표준편차 (Pooled Standard Deviation)**로 계산됩니다:

$$
S_p = \sqrt{\dfrac{(n_1 - 1) S_1^2 + (n_2 - 1) S_2^2}{n_1 + n_2 - 2}}
$$

**등분산을 가정하지 않는** 경우 (**Welch의 t-검정**), 검정 통계량은 다음과 같습니다:

$$
t = \frac{\bar{X}_1 - \bar{X}_2}{\sqrt{\dfrac{S_1^2}{n_1} + \dfrac{S_2^2}{n_2}}}
$$

이 경우 자유도 (df)는 다음과 같이 계산됩니다:

$$
df = \dfrac{\left( \dfrac{S_1^2}{n_1} + \dfrac{S_2^2}{n_2} \right)^2}{\dfrac{\left( \dfrac{S_1^2}{n_1} \right)^2}{n_1 - 1} + \dfrac{\left( \dfrac{S_2^2}{n_2} \right)^2}{n_2 - 1}}
$$

---

## **3. 정규성 검정 (Normality Test)**

t-검정을 적용하기 전에 데이터가 정규분포를 따르는지 확인해야 합니다. 이를 위해 다음과 같은 방법을 사용합니다:

- **시각적 방법**:
    - **Q-Q 플롯 (Q-Q Plot)**: 데이터 분포와 정규분포를 비교하여 직선 형태로 나타나는지 확인합니다.
    - **히스토그램 (Histogram)**: 데이터의 전체적인 분포 모양을 확인합니다.
- **통계적 방법**:
    - **Shapiro-Wilk 검정**: 표본 크기가 작을 때 유용하며, 데이터가 정규분포를 따르는지 검정합니다.
    - **Kolmogorov-Smirnov 검정**: 표본 크기가 클 때 사용하지만, 민감도가 낮을 수 있습니다.

**R 코드 예시**:

```R
# Shapiro-Wilk 정규성 검정
shapiro.test(group1)
shapiro.test(group2)

# Q-Q 플롯
qqnorm(group1); qqline(group1)
qqnorm(group2); qqline(group2)
```

---

## **4. 등분산성 검정 (Equality of Variances Test)**

두 집단의 분산이 동일한지 확인하는 과정입니다. 주요 방법은 다음과 같습니다:

- **F-검정 (F-test)**: 두 집단의 분산을 비교합니다.
- **Levene의 검정 (Levene's Test)**: 데이터가 정규분포를 따르지 않을 때 사용합니다.

**R 코드 예시**:

```R
# F-검정
var.test(group1, group2)

# Levene's Test
install.packages("car")  # car 패키지 설치 (한번만 실행)
library(car)
leveneTest(c(group1, group2), group = rep(c("Group1", "Group2"), each = length(group1)))
```

---

## **5. 두 표본 t-검정 실시**

**R 코드 예시**:

```R
# 등분산을 가정한 t-검정
t.test(group1, group2, var.equal = TRUE)

# 등분산을 가정하지 않은 t-검정 (Welch의 t-검정)
t.test(group1, group2, var.equal = FALSE)
```

---

## **6. 결과 해석**

- **p-값 해석**:
    - **p-값 < 유의수준 (예: 0.05)**: 귀무가설을 기각하고, 두 집단의 평균이 유의하게 다르다고 결론 내립니다.
    - **p-값 ≥ 유의수준**: 귀무가설을 기각할 수 없으며, 두 집단의 평균 차이가 통계적으로 유의하지 않다고 판단합니다.
- **신뢰구간**: 평균 차이에 대한 신뢰구간을 확인하여 효과의 크기와 방향을 파악합니다.

---

## **7. 실용적인 예시**

**상황**: 새로운 교육 프로그램이 학생들의 성적에 미치는 영향을 평가하고자 합니다.

- **대조군 (Control Group)**: 기존 교육을 받은 20명의 학생 성적.
- **처리군 (Treatment Group)**: 새로운 교육을 받은 20명의 학생 성적.

**데이터 입력**:

```R
# 대조군 성적
control_scores <- c(75, 78, 74, 77, 73, 76, 72, 79, 71, 74, 76, 75, 77, 73, 78, 72, 74, 76, 75, 77)

# 처리군 성적
treatment_scores <- c(80, 82, 81, 83, 79, 84, 80, 85, 81, 83, 82, 84, 80, 85, 81, 83, 82, 84, 80, 85)
```

**정규성 검정**:

```R
# Shapiro-Wilk 정규성 검정
shapiro.test(control_scores)
shapiro.test(treatment_scores)
```

**등분산성 검정**:

```R
# F-검정
var.test(control_scores, treatment_scores)
```

**t-검정 실시**:

```R
# 등분산을 가정한 t-검정
t.test(control_scores, treatment_scores, var.equal = TRUE)
```

**결과 해석**:

- **정규성 검정 결과**: 두 집단 모두 정규성을 만족한다고 판단되면 t-검정을 진행합니다.
- **등분산성 검정 결과**: 등분산을 가정할 수 있으면 등분산 t-검정을 사용합니다.
- **t-검정 결과**: p-값이 유의수준보다 작으면 새로운 교육 프로그램이 유의미한 영향을 미쳤다고 결론 내립니다.

---

## **8. 독립성과 불편추정량 (Independence and Unbiased Estimator)**

### **불편추정량이란?**

**추정량**은 모집단의 특성을 추정하기 위해 표본 데이터를 사용하여 계산되는 통계량입니다.  
**불편추정량 (Unbiased Estimator)**은 그러한 추정량 중에서 그 기대값이 실제 모집단의 모수와 같은 것을 말합니다.  
즉, 여러 번 표본을 추출하여 추정량을 계산하면 그 평균이 모집단의 실제 값에 수렴한다는 의미입니다.

- **예시**: 표본 평균 는 모집단 평균 의 불편추정량입니다.

### **독립성이 왜 중요한가요?**

**독립성**은 표본 내의 관측치들이 서로 영향을 주지 않는다는 가정입니다. 이 가정이 중요한 이유는 다음과 같습니다:

- **불편성 보장**: 관측치들이 독립적일 때, 추정량의 기대값이 모집단 모수와 동일하게 되어 불편성을 보장합니다.
- **분산 계산의 정확성**: 독립성을 통해 표본 평균이나 분산의 계산 공식이 유효해집니다. 만약 관측치들이 상관되어 있다면, 분산이 잘못 계산되어 검정 결과가 신뢰할 수 없게 됩니다.

### **독립성이 위배되면 어떤 문제가 발생하나요?**

- **편향된 추정량**: 관측치들이 독립적이지 않으면 추정량이 실제 모집단 모수에서 벗어나게 됩니다.
- **잘못된 결론**: 분산이 과소 또는 과대 추정되어 통계적 검정에서 오류가 발생할 수 있습니다. 이는 **제1종 오류 (false positive)**나 **제2종 오류 (false negative)**를 증가시킵니다.

### **실생활 예시**

- **온라인 실험에서의 문제점**: 동일한 사용자가 여러 번 방문하여 데이터를 생성하면 관측치들이 독립적이지 않습니다. 이를 무시하고 분석하면 결과가 왜곡될 수 있습니다.
- **해결 방법**: 데이터 수집 단계에서 관측치의 독립성을 확보하거나, 분석 단계에서 상관성을 고려한 통계 기법을 사용합니다.

---

## **9. 추가 고려사항 및 주의사항**

- **정규성 위배 시 대처 방법**:
    - **데이터 변환**: 로그 변환, 제곱근 변환 등을 통해 정규성에 가까워지도록 합니다.
    - **비모수 검정**: Mann-Whitney U 검정 등 정규성 가정을 필요로 하지 않는 검정을 사용합니다.
- **이상치 (Outliers) 처리**:
    - **이상치 식별**: 박스플롯 등을 사용하여 이상치를 식별합니다.
    - **처리 방법**: 이상치를 제거하거나, 분석 방법을 변경하여 이상치의 영향을 줄입니다.
- **데이터의 단위 일관성**:
    - 실험의 단위 (예: 사용자 단위, 세션 단위)와 분석의 단위를 일치시켜야 합니다.
    - 단위가 일치하지 않으면 독립성 가정이 붕괴될 수 있습니다.

---

**참고 문헌**

- Lehmann, E. L., & Romano, J. P. (2005). *Testing Statistical Hypotheses*. Springer.
- Casella, G., & Berger, R. L. (2001). *Statistical Inference*. Duxbury.
- Wasserman, L. (2004). *All of Statistics*. Springer.
- Razali, N. M., & Wah, Y. B. (2011). Power comparisons of Shapiro-Wilk, Kolmogorov-Smirnov, Lilliefors, and Anderson-Darling tests. *Journal of Statistical Modeling and Analytics*, 2(1), 21-33.
- Efron, B., & Tibshirani, R. J. (1994). *An Introduction to the Bootstrap*. CRC Press.
- - **Trustworthy Online Controlled Experiments**