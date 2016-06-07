**1. Write a function to swap a number in place (that is, without temporary variables).**      
```
static void swapNumber(int a, int b){
		  System.out.println("Before swapping: a = " + a + ", b = " + b);
		  
		  a = a+b;
		  b = a-b;
		  a = a-b;
		  
		  System.out.println("After swapping: a = " + a + ", b = " + b);
		  
		  a = a^b;
		  b = a^b;
		  a = a^b;
		  
		  System.out.println("Swapping back with bit manipulation: a = " + a + ", b = " + b);
	}
```

**2. Design a method to find the frequency of occurrences of any given word in a book. What if we were running this algorithm multiple times?**     
```
  //Single Query
	static int getWordFrequency(String[] book, String word){
		int count = 0;
		word = word.trim().toLowerCase();
		
		for(String w:book){
			if(w.trim().toLowerCase().equals(word)){
				count++;
			}
		}
		
		return count;
	}
	
	//Repetitive Queries
	static int getWordFrequency(HashMap<String,Integer> table, String word){
		
		if(table == null || word == null){
			return -1;
		}
		
		word = word.trim().toLowerCase();
		
		if(table.containsKey(word)){
			return table.get(word);
		}
		
		return 0;
	}
	
	static HashMap<String, Integer> setWordDictionary(String[] book){
		HashMap<String,Integer> table = new HashMap<String,Integer>();
		
		for(String word:book){
			word = word.trim().toLowerCase();
			
			if(word != ""){
				if(!table.containsKey(word)){
					table.put(word, 0);
				}
				
				table.put(word, table.get(word)+1);
			}
		}
		
		return table;
	}
```

**3. Write an algorithm which computes the number of trailing zeros in n factorial.**

A simple approach is to compute the factorial, and then count the number of trailing zeros by continuously dividing by ten. The problem
with this though is that the bounds of an int would be exceeded very quickly.

A trailing zero is created with multiples of 10, and multiples of 10 are created with pairs of 5-multiples and 2-multiples. Therefore, to count
the number of zeros, we only need to count the pairs of multiples of 5 and 2. There will always be more multiples of 2 than 5, though, so
simply counting the number of multiples of 5 is sufficient.

```
//Solution 1:
int factorsOf5(int num){
  int count = 0;
  
  while(num % 5 == 0){
    count++;
    num /= 5;
  }
  
  return 5;
}

int countFactorialZeros(int num){
  int count = 0;
  
  for(int i = 2; i <= num; i++){
    count += factorsOf5(i);
  }
  
  return count;
}

```
We can make it a little more efficient by directly counting the factors of 5. Using this approach, we would first count the number of multiples
of 5 between 1 and n (which is n/5), then the number of multiples of 25 (n/25), then 125, and so on.   

```
//Solution 2:
int countFactorialZeros(int num){
  int count = 0;
  if(num < 0){
    return -1;
  }
  
  for(int i = 5; num/i > 0; i *= 5){
    count += num/i;
  }
  
  return count;
}
```

**4. Given two arrays of integers, compute the pair of values (one value in each array) with the smallest (non-negative) difference. Return the difference.**      
```
int findSmallestDifference(int[] a, int[] b){
  if(a.length == 0 || b.length == 0){
    return -1;
  }
  
  int min = Integer.MAX_VALUE;
  
  for(int i = 0; i < a.length; i++){
    for(int j = 0; j < b.length; j++){
      int diff = Math.abs(a[i] - b[j]);
      
      if(diff < min){
        min = diff;
      }
    }
  }
  
  return min;
}
```

**5. Write a method that finds the maximum of two numbers. You should not use if-else or any other comparison operator**      
```
int getMax(int a, int b){
  int c = a - b;
  
  int sa = sign(a); //if a >= 0, then 1 else 0
  int sb = sign(b); //if b >= 0, then 1 else 0
  int sc = sign(c); //depends on whether or not a - b overflows
  
  /*Goal: define a value k which is 1 if a > b and 0 if a < b. If a=b, it does not matter what k is*/
  
  //If a and b have different signs, then k = sign(a)
  int use_sign_of_a = sa^sb;
  
  //If a and b have the same sign, then k = sign(a - b)
  int use_sign_of_c = flip(sa^sb);
  
  int k = use_sign_of_a*sa + use_sign_of_c*sc;
  int q = flip(k); //opposite of k
  
  return a*k + b*q;
}

/*Returns 1 if a is positive, and 0 if a is negative*/
int sign(int a){
  return flip((a>>31) & 0x1);
}

/*Flips a 1 to 0 or a 0 to 1*/
int flip(int bit){
  return 1^bit;
}

```

**6. Write methods to implement the multiply, subtract, and divide operations for integers. The results of all of these are integers.
Use only the add operator.**           

```
/*Flip a positive sign to negative or negative sign to positive*/
int negate(int a){
  int neg = 0;
  
  int newSign = (a > 0)?-1:1;
  while(a != 0){
    neg += newSign;
    a += newSign;
  }
  
  return neg;
}

/*Subtract two numbers by negating b and adding them*/
int minus(int a, int b){
  return a + negate(b);
}

int abs(int a){
  if(a >= 0){
    return a;
  }else{
    return negate(a);
  }
}

/*Multiply a by b by adding a to itself b times*/
int multiply(int a, int b){
  int sum = 0;
  for(int i = 0; i<abs(b); i++){
    sum += a;
  }
  
  if(b < 0){
    sum = negate(sum);
  }
  
  return sum;
}

int divide(int a, int b){
  if(b == 0){
    throw new java.lang.ArithmetricException("ERROR");
  }
  
  int absa = abs(a);
  int absb = abs(b);
  int x = 0;
  
  while(absa != multiply(absb,x)){
    x++;
  }
  
  if((a < 0 && b <0)||(a > 0 && b > 0)){
    return x;
  }else{
    return negate(x);
  }
}

```

**7. Given two arrays of integers, find a pair of values (one value from each array) that you can swap to give the two arrays the same sum.**    
```
int[] findSwapPair(int[] array1, int[] array2){
  Integer target = getTarget(array1,array2);
  if(target == null){
    return null;
  }
  
  for(int one:array1){
    for(int two:array2){
      if(one - two == target){
        int[] values = {one,two};
        return values;
      }
    }
  }
  
  return null;
}

Integer getTarget(int[] array1, int[] array2){
  int sum1 = sum(array1);
  int sum2 = sum(array2);
  
  if((sum1 -sum2) % 2 != 0){
    return null;
  }
  
  return (sum1 - sum2)/2;
}
```

**8. Write a function that adds two numbers. You should not use + or any arithmetic operators.**
```
int add(int a, int b){
  if(b == 0) return a;
  
  int sum = a^b; //add without carrying
  int carry = (a & b) << 1; //carry, but do not add
  
  return add(sum,carry); //recurse with sum + carry
}
```

**9. Write a method to shuffle a deck of cards. It must be a perfect shuffle--in other words, each of the 52! permutations of the deck has to
be equally likely. Assume that you are given a random number generator which is perfect**   
```
/*Random number between lower and higher, inclusive*/
int rand(int lower, int higher){
  return lower + (int)(Math.random()*(higher - lower + 1));
}

int[] shuffleArrayRecursively(int[] cards, int i){
  if(i == 0) return cards;
  
  shuffleArrayRecursively(cards, i-1);
  
  int k = rand(0,i);
  
  /*Swap element k and i*/
  int temp = cards[k];
  cards[k] = cards[i];
  cards[i] = temp;
  
  /*return shuffled array*/
  return cards;
}

int[] shuffleArrayIteratively(int[] cards){
  for(int i = 0; i < cards.length; i++){
    int random = rand(0,i);
    int temp = cards[random];
    cards[random] = cards[i];
    cards[i] = temp;
  }
  
  return cards;
}
```

**10. Write a method to randomly generate a set of m integers from an array size of n. Each element must have equal probability of being chosen.**    
```
int pickMRandomNumber(int[] array, int m){
  int[] subset = new int[m];
  
  /*Fill in subset aray with first part of original array*/
  for(int i = 0; i < m; i++){
    subset[i] = array[i];
  }
  
  /*Go through rest of original array*/
  for(int i = m; i < array.length; i++){
    int k = rand(0,i);
    
    if(k < m){
      subse[k] = array[i];
    }
  }
}
```
