```py
def twoNumberSum(array, targetSum):
  results = {} # C1 1 Time
  for num in array: #C2 N time
    y = targetSum - num #C3 1 time
    if y in results: #C4 1 time
      return [y, num] #C5 1 time
     else:
      results[num] = True #C6 1 time
  return [] #C7 1 time
```
