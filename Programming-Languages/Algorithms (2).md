**1. Given an array filled with letters and numbers, find the longest subarray with an equal number of letters and numbers.**
```
char[] findLongestSubarray(char[] array){
  for(int len = array.length; len > 1; len--){
    for(int i = 0; i <= array.length - len; i++){
      if(hasEqualLettersNumbers(array,i,i+len-1)){
        return extractSubarray(array,i,i+len-1);
      }
    }
  }
  
  return null;
}

boolean hasEqualLettersNumbers(char[] array, int start, int end){
  int counter = 0;
  for(int i = start; i <= end; i++){
    if(Character.isLetter(array[i])){
      counter++;
    }else{
      counter--;
    }
  }
  
  return counter == 0;
}

char[] extractSubarray(char[], int start, int end){
  char[] subarray = new char[end - start + 1];
  
  for(int i = start; i <= end; i++){
    subarray[i-start] = array[i];
  }
  
  return subarray;
}
```

**2. Write a method to count the number of 2s between 0 and n.**
```
int numberOf2sInRange(int n){
  int count = 0;
  
  for(int i = 2; i <= n; i++){
    count += numberOf2s(i);
  }
}

int numberOf2s(int n){
  int count = 0;
  
  while(n > 0){
    if(n%10 == 2){
      count++;
    }
    
    n /= 10;
  }
  
  return count;
}
```

**3. Design an glogrithm to find the smalllest K numbers in an array.**
```
//Solution 1: Sorting
int smallestK(int[] array, int k){
  if(k <= 0 || k > array.length){
    throw new IllegalArgumentException();
  }
  
  /*Sort array*/
  Arrays.sort(array);
  
  /*Copy first K elements*/
  int[] smallest = new int[k];
  for(int i = 0; i < k; i++){
    smallest[i] = array[i];
  }
  
  return smallest;
}
```

```
//Solution 2: max heap
int[] smallestK(int[] array, int k){
  if(k <= 0 || k > array.length){
    throw new IllegalArgumentException();
  }
  
  PriorityQueue<Integer> heap = getKMaxHeap(array,k);
  return heapToIntArray(heap);
}

/*Create a max heap of smallest k elements*/
PriorityQueue<Integer> getKMaxHeap(int[] array, int k){
  PriorityQueue<Integer> heap = new PriorityQueue<Integer>(k, new MaxHeapComparator());
  
  for(int a : array){
    if(heap.size() < k){ //If space remaining
      heap.add(a);
    }else if(a < heap.peek()){ //If full and top is small
      heap.poll();//Remove highest
      heap.add(a);
    }
  }
  
  return heap;
}

/*Convert heap to int array*/
int[] heapToIntArray(PriorityQueue<Integer> heap){
  int[] array = new int[heap.size()];
  while(!heap.isEmpty()){
    array[heap.size() - 1] = heap.poll();
  }
  return array;
}

class MaxHeapComparator implements Comparator<Integer>{
  public int compare(Integer x, Integer y){
    return y - x;
  }
}
```

**4. Given a list of words, write a program to find the longest word made of other words in the list.**
```
String printLongestWord(String[] arr){
  HashMap<String, Boolean> map = new HashMap<>(String,Boolean);
  
  for(String str:arr){
    map.put(str, true);
  }
  
  Arrays.sort(arr, new LengthComparator());//Sort by length
  
  for(String s:arr){
    if(canBuildWord(s,true,map)){
      System.out.println(s);
      return s;
    }
  }
  
  return "";
}

boolean canBuildWord(String str, boolean isOriginalWord, HashMap<String, Boolean> map){
  if(map.containsKey(str) && !isOriginalWord){
    return map.get(str);
  }
  
  for(int i = 1; i < str.length(); i++){
    String left = str.substr(0,i);
    String right = str.substr(i);
    
    if(map.containsKey(left) && map.get(left) == true && canBuildWord(right,false,map)){
      return true;
    }
  }
  
  map.put(str, false);
  return false;
}
```

**5. Reverse a Linked list**     
[Print Reverse](http://www.geeksforgeeks.org/write-a-recursive-function-to-print-reverse-of-a-linked-list/)      
[Reverse](http://www.geeksforgeeks.org/write-a-function-to-reverse-the-nodes-of-a-linked-list/)
