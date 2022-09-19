```py
def twoNumberSum(array, targetSum):
  results = {} # C1 1 Time
  for num in array: #C2 N time
    y = targetSum - num #C3 N time
    if y in results: #C4 N time
      return [y, num] #C5 1 time
     else:
      results[num] = True #C6 N time
  return [] #C7 1 time
```

$$  C_{1} + N(C_{2} + C_{3} + C_{4} + C_{6}) + C_{5} + C_{7}  $$


$$  C_{1} +  C_{5} + C_{7} + N(C_{2} + C_{3} + C_{4} + C_{6})  $$

$$ \alpha + N\beta $$

