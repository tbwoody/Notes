As you probably already know, the number of possible different combinations is 2^n, where n equals the length of the input string.    

Since n could theoretically be fairly large, there's a chance that 2^n will exceed the capacity of a primitive type such as an int. (The user may have to wait a few years for all of the combinations to finish printing, but that's their business.)    

Instead, let's use a bit vector to hold all of the possible combinations. We'll set the number of bits equal to n and initialize them all to 1. For example, if the input string is "abcdefghij", the initial bit vector values will be {1111111111}.    

For every combination, we simply have to loop through all of the characters in the input string and set each one to uppercase if its corresponding bit is a 1, else set it to lowercase. We then decrement the bit vector and repeat.     

For example, the process would look like this for an input of "abc":     

Bits:   Corresponding Combo:      
111    ABC     
110    ABc     
101    AbC     
100    Abc    
011    aBC    
010    aBc     
001    abC     
000    abc    

By using a loop rather than a recursive function call, we also avoid the possibility of a stack overflow exception occurring on large input strings.       

Here is the actual implementation:     

```
import java.util.BitSet;

public void PrintCombinations(String input) {
    char[] currentCombo = input.toCharArray();

    // Create a bit vector the same length as the input, and set all of the bits to 1
    BitSet bv = new BitSet(input.length());
    bv.set(0, currentCombo.length);

    // While the bit vector still has some bits set
    while(!bv.isEmpty()) {
        // Loop through the array of characters and set each one to uppercase or lowercase, 
        // depending on whether its corresponding bit is set
        for(int i = 0; i < currentCombo.length; ++i) {
            if(bv.get(i)) // If the bit is set
                currentCombo[i] = Character.toUpperCase(currentCombo[i]);
            else
                currentCombo[i] = Character.toLowerCase(currentCombo[i]);
        }

        // Print the current combination
        System.out.println(currentCombo);

        // Decrement the bit vector
        DecrementBitVector(bv, currentCombo.length);            
    }

    // Now the bit vector contains all zeroes, which corresponds to all of the letters being lowercase.
    // Simply print the input as lowercase for the final combination
    System.out.println(input.toLowerCase());        
}


public void DecrementBitVector(BitSet bv, int numberOfBits) {
    int currentBit = numberOfBits - 1;          
    while(currentBit >= 0) {
        bv.flip(currentBit);

        // If the bit became a 0 when we flipped it, then we're done. 
        // Otherwise we have to continue flipping bits
        if(!bv.get(currentBit))
            break;
        currentBit--;
    }
}
```
