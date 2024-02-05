# GameDev A/B test

## Introduction/Background Context
We have changed the icon for buying event products in our game.  We want to understand how this will affect user engagement and spending
### Hypothesis    
H0: No change    
H1: There is a change

## Experimental Design

| Group                  | Size of Events | Size of Distinct Users |
|------------------------|----------------|------------------------|
| Control                | 558,070        | 89,034                 |
| Test                   | 138,770        | 22,278                 |

timeline - 1 month    
alpha - unknown    
MDE - unkown    

## Analysis
After conducting a sanity check, verified that the samples are identical, it is unlikely that any other factors affected user spending

We have a lognormal distribution of spending, for it the CPT does not work,even after logarithmization     
  <div style="display: flex; justify-content: space-between;">
      <img src="https://github.com/apereprosov/ab-test-gamedev/assets/61319269/44b2139a-7318-4815-83f2-a551761caf25" style="flex: 1; object-fit: cover; width: 40%; height: 60%;" />
      <img src="https://github.com/apereprosov/ab-test-gamedev/assets/61319269/d656a7d8-526c-40b1-bbb5-acb7282c8cc3" style="flex: 1; object-fit: cover; width: 40%; height: 60%;" />
  </div>    

I chose **Bootstrap** as a tool and accelerated it in **10x** times in workflow using backets   

## Metrics
### CTR
| Group  | Mean   | 
|--------|--------|
| Control| 0.06523|
| Test   | 0.06492|
### ARPPU
| Group  | Mean   | Median |
|--------|--------|--------|
| Control| 1965.21| 1000   |
| Test   | 2104.61| 1500   |
### ARPU
| Group  | Mean   | Median |
|--------|--------|--------|
| Control| 128.191087	| 0   |
| Test   | 136.631837 | 0   |

## Bootstrap Improvment
Default Bootstrap Example
```python
n = 1000
result = []
base_diff = 136.631837 - 128.191087

for _ in tqdm(range(n)):
  a = df['gold_on_event_nona'].sample(group_size[0],replace=True).mean()
  b = df['gold_on_event_nona'].sample(group_size[1],replace=True).mean()
  result.append(np.abs(a-b) >= base_diff)
```
Transforming into backets Example
```python
# observation per basket (common denominator)
b_n = 10

#baskets per group
n1 = int(df['AbGroup'].value_counts()[0] / b_n)
n2 = int(df['AbGroup'].value_counts()[1] / b_n)

test = df.sort_values('AbGroup').copy()
test['bucket'] = [i for i in range(n1)]*b_n + [i for i in range(n2)] * b_n

bucketed_nona = test.groupby(by=["bucket","AbGroup"])["gold_on_event_nona"].agg(['mean','std']).reset_index()
```
Mean and variance in this variant remain roughly similar
| Group  | Mean   | Std |
|--------|--------|--------|
| Default| 129.871994	| 1109.449419   |
| Backet   | 129.871994 | 1047.719873  |

# Results 
| Metric                   | ARPU            | ARPPU          |
|--------------------------|-----------------|----------------|
| p-value                  | 0.01            | 0.0054         |
| for distinct users       | 0.0             | 0.0224         |

