## Human readable duration format
https://www.codewars.com/kata/52742f58faf5485cae000b9a
```csharp
using System;
using System.Text;
using System.Collections.Generic;

public class HumanTimeFormat{
  public static string formatDuration(int seconds)
  {
            StringBuilder builder = new StringBuilder();
            var UnitsList = new List<string>();

            if (seconds == 0) return "now";

            //years
            if (seconds >= 31536000 && seconds < 31536000 * 2 - 1) UnitsList.Add("1 year");
            else if (seconds >= 31536000 * 2) UnitsList.Add($"{seconds / 31536000} years");

            //days
            if (seconds % 31536000 >= 86400 && seconds % 31536000 < 86400 * 2 - 1) UnitsList.Add("1 day");
            else if (seconds % 31536000 >= 86400 * 2) UnitsList.Add($"{seconds % 31536000 / 86400} days");

            //hours
            if (seconds % 31536000 % 86400 >= 3600 && seconds % 31536000 % 86400 < 3600 * 2 - 1) UnitsList.Add("1 hour");
            else if (seconds % 31536000 % 86400 >= 3600 * 2) UnitsList.Add($"{seconds % 31536000 % 86400 / 3600} hours");

            //minutes
            if (seconds % 31536000 % 86400 % 3600 >= 60 && seconds % 31536000 % 86400 % 3600 < 60 * 2 - 1) UnitsList.Add("1 minute");
            else if (seconds % 31536000 % 86400 % 3600 >= 60 * 2) UnitsList.Add($"{seconds % 31536000 % 86400 % 3600 / 60} minutes");

            //seconds
            if (seconds % 31536000 % 86400 % 3600 % 60 == 1) UnitsList.Add("1 second");
            else if (seconds % 31536000 % 86400 % 3600 % 60 > 1) UnitsList.Add($"{seconds % 31536000 % 86400 % 3600 % 60} seconds");

            if (UnitsList.Count == 1) return UnitsList[0].ToString();
            else if (UnitsList.Count == 2) return $"{UnitsList[0].ToString()} and {UnitsList[1].ToString()}";
            else if (UnitsList.Count > 2)
            {
                for (int i = 0; i < UnitsList.Count; i++)
                {
                    if (i == UnitsList.Count - 1) builder.Append(" and ");
                    builder.Append(UnitsList[i]);
                    if (i < UnitsList.Count - 2) builder.Append(", ");

                }
            }
            return builder.ToString();
  }
}
```


## Decode the Morse code, advanced
https://www.codewars.com/kata/54b72c16cd7f5154e9000457

```csharp
using System;
using System.Text.RegularExpressions;
using System.Text;
using System.Collections.Generic;

public class MorseCodeDecoder
{
    public static string DecodeBits(string bits)
    {
                bool startQuiet = true;
                bool lastCharacterZero = false;
                StringBuilder binaryWithNoExtraZeros = new StringBuilder();
                StringBuilder finalMorseCode = new StringBuilder();                
                int oneCounter = 0;
                int zeroCounter = 0;
                int speedMultiplier = 1;
                int counter = 0;
                int longerOneSequence = 0;
                int shorterOneSequence = 0;
                int shorterZeroSequence = 0;
                int middleZeroSequence = 0;
                int longerZeroSequence = 0;
                

                //removes any extra zeroes at the beggining and end of bits
                for(int i = 0; i < bits.Length; i++)
                {
                    if (bits[i] == '1')
                    {
                        startQuiet = false;
                        lastCharacterZero = false;
                        counter = 0;
                        binaryWithNoExtraZeros.Append('1');
                    }
                    if(bits[i] == '0' && startQuiet == false)
                    {
                        lastCharacterZero = true;
                        binaryWithNoExtraZeros.Append('0');
                        counter++;
                    }
                }
                if (lastCharacterZero == true) binaryWithNoExtraZeros.Remove(binaryWithNoExtraZeros.Length - counter, counter);



                //finding speedMultiplier
                if (!binaryWithNoExtraZeros.ToString().Contains('0'))
                {
                    speedMultiplier = binaryWithNoExtraZeros.Length;                   
                }
                else
                {
                    for(int i = 0; i < binaryWithNoExtraZeros.Length; i++)
                    {
                        if(binaryWithNoExtraZeros[i] == '1')
                        {
                            if (zeroCounter > longerZeroSequence)
                            {
                                shorterZeroSequence = middleZeroSequence;
                                middleZeroSequence = longerZeroSequence;
                                longerZeroSequence = zeroCounter;
                            }
                            if(i == binaryWithNoExtraZeros.Length - 1)
                            {
                                if (oneCounter > longerOneSequence)
                                {
                                    shorterOneSequence = longerOneSequence;
                                    longerOneSequence = oneCounter;
                                }
                            }
                            zeroCounter = 0;
                            oneCounter++;
                        }
                        if(binaryWithNoExtraZeros[i] == '0')
                        {
                            if(oneCounter > longerOneSequence)
                            {
                                shorterOneSequence = longerOneSequence;
                                longerOneSequence = oneCounter;
                            }
                            oneCounter = 0;
                            zeroCounter++;
                        }
                    }
                    if (longerOneSequence == shorterOneSequence * 3) speedMultiplier = shorterOneSequence;
                    else if (longerOneSequence == longerZeroSequence) speedMultiplier = longerOneSequence;
                    else if (longerZeroSequence == longerOneSequence * 3) speedMultiplier = longerOneSequence;
                    else if (longerZeroSequence == longerOneSequence * 7) speedMultiplier = longerOneSequence;
                    else if (longerOneSequence == longerZeroSequence * 3) speedMultiplier = longerZeroSequence; 
                    else if (longerOneSequence * 7 == longerZeroSequence * 3) speedMultiplier = longerOneSequence / 3;
                }

                //converting to morse code
                oneCounter = 0;
                zeroCounter = 0;
                for (int i = 0; i < binaryWithNoExtraZeros.Length; i++)
                {                                     
                    if (binaryWithNoExtraZeros[i] == '1')
                    {
                        oneCounter++;
                        if (zeroCounter / speedMultiplier == 3) finalMorseCode.Append(' ');
                        if (zeroCounter / speedMultiplier == 7) finalMorseCode.Append("   ");
                        zeroCounter = 0;
                        if(i == binaryWithNoExtraZeros.Length-1)
                        {
                            if (oneCounter / speedMultiplier == 1) finalMorseCode.Append('.');
                            if (oneCounter / speedMultiplier == 3) finalMorseCode.Append('-');
                        }
                    }
                    if (binaryWithNoExtraZeros[i] == '0')
                    {
                        zeroCounter++;
                        if (oneCounter / speedMultiplier == 1) finalMorseCode.Append('.');
                        if (oneCounter / speedMultiplier == 3) finalMorseCode.Append('-');
                        oneCounter = 0;
                    }
                }
                return finalMorseCode.ToString();
    }

    public static string DecodeMorse(string morseCode)
    {
        var tupleList = new List<Tuple<string, string>>
                {
                    Tuple.Create("A",".-"),
                    Tuple.Create("B","-..."),
                    Tuple.Create("C","-.-."),
                    Tuple.Create("D","-.."),
                    Tuple.Create("E","."),
                    Tuple.Create("F","..-."),
                    Tuple.Create("G","--."),
                    Tuple.Create("H","...."),
                    Tuple.Create("I",".."),
                    Tuple.Create("J",".---"),
                    Tuple.Create("K","-.-"),
                    Tuple.Create("L",".-.."),
                    Tuple.Create("M","--"),
                    Tuple.Create("N","-."),
                    Tuple.Create("O","---"),
                    Tuple.Create("P",".--."),
                    Tuple.Create("Q","--.-"),
                    Tuple.Create("R",".-."),
                    Tuple.Create("S","..."),
                    Tuple.Create("T","-"),
                    Tuple.Create("U","..-"),
                    Tuple.Create("V","...-"),
                    Tuple.Create("W",".--"),
                    Tuple.Create("X","-..-"),
                    Tuple.Create("Y","-.--"),
                    Tuple.Create("Z","--.."),
                    Tuple.Create("1",".----"),
                    Tuple.Create("2","..---"),
                    Tuple.Create("3","...--"),
                    Tuple.Create("4","....--"),
                    Tuple.Create("5","....."),
                    Tuple.Create("6","-...."),
                    Tuple.Create("7","--..."),
                    Tuple.Create("8","---.."),
                    Tuple.Create("9","----."),
                    Tuple.Create("0","-----"),
                    Tuple.Create("?","..--.."),
                    Tuple.Create("!","-.-.--"),
                    Tuple.Create(".",".-.-.-"),
                    Tuple.Create(",","--..--"),
                    Tuple.Create(";","-.-.-."),
                    Tuple.Create(":","---..."),
                    Tuple.Create("+",".-.-."),
                    Tuple.Create("-","-....-"),
                    Tuple.Create("/","-..-."),
                    Tuple.Create("=","-...-"),
                    Tuple.Create("SOS","...---...")
                };
                StringBuilder oneCharacter = new StringBuilder();
                StringBuilder finalString = new StringBuilder();
                int spaceCounter = 0;               
                bool lastSpace = false;

                

                for(int i = 0; i < morseCode.Length; i++) 
                {
                    if (morseCode[i] != ' ')
                    {
                        lastSpace = false;
                        spaceCounter = 0;
                        oneCharacter.Append(morseCode[i]);  
                        if(i == morseCode.Length - 1)
                        {
                            for (int j = 0; j < tupleList.Count; j++)
                            {                              
                                if (oneCharacter.ToString() == tupleList[j].Item2)
                                {
                                    finalString.Append(tupleList[j].Item1);
                                    oneCharacter.Clear();
                                    break;

                                }
                            }
                        }
                    }
                    else
                    {
                        spaceCounter++;
                        for (int j = 0; j < tupleList.Count; j++)
                        {
                            
                            if(spaceCounter % 3 == 0 && finalString.Length != 0)
                            {
                                finalString.Append(' ');                               
                                oneCharacter.Clear();
                                lastSpace = true;
                                break;
                            }
                            if(oneCharacter.ToString() == tupleList[j].Item2)
                            {
                                finalString.Append(tupleList[j].Item1);
                                oneCharacter.Clear();
                                break;
                                
                            }
                        }
                    }
                }
                if (lastSpace == true) finalString.Remove(finalString.Length-spaceCounter/3, (spaceCounter / 3));
                return finalString.ToString();
    }
}
```


## Count IP Addresses
https://www.codewars.com/kata/526989a41034285187000de4

```csharp
using System;
public class CountIPAddresses
{
   public static long IpsBetween(string start, string end)
   {
                string[] startSplit = start.Split('.');
                string[] endSplit = end.Split('.');
                int[] startInt = new int[4];
                int[] endInt = new int[4];
                long result = 1;
                
                for(int i = 0; i < 4; i++)
                {
                    startInt[i] = Int32.Parse(startSplit[i]);
                    endInt[i] = Int32.Parse(endSplit[i]);
                }

                while((startInt[0] == endInt[0] && startInt[1] == endInt[1] && startInt[2] == endInt[2] && startInt[3] == endInt[3]) == false)
                {
                    result++;
                    startInt[3] = startInt[3] + 1;
                    if (startInt[3] == 256)
                    {                       
                        startInt[3] = 0;
                        startInt[2]++;
                    }
                    if (startInt[2] == 256)
                    {                       
                        startInt[2] = 0;
                        startInt[1]++;
                    }
                    if (startInt[1] == 256)
                    {                        
                        startInt[1] = 0;
                        startInt[0]++;
                    }
                }
                return result-1;
   }
}
```


## Sum of Intervals
https://www.codewars.com/kata/52b7ed099cdc285c300001cd

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Intervals
{
    public static int SumIntervals((int, int)[] intervals)
    {
        int[][] arr = new int[intervals.Length][];
                
                for(int i = 0; i < intervals.Length; i++)
                {
                    int one = intervals[i].Item1;
                    int two = intervals[i].Item2;
                    arr[i] = new int[] { one, two };
                }
                
                List<int[]> list = new List<int[]>();
                bool changed = true;
                bool merged = false;
                int total = 0;

                for (int i = 0; i < intervals.Length; i++)
                {
                    list.Add(arr[i]);
                }

                while (changed == true)
                {
                    list = FindDistinct(list);
                    list = MergeIntervals(list);
                }
                list = FindDistinct(list);

                int[] oneRow = new int[2];

                for (int i = 0; i < list.Count; i++)
                {
                    for (int j = 0; j < 2; j++)
                    {
                        oneRow[j] = list[i][j];
                    }
                    total = total + (oneRow[1] - oneRow[0]);
                }

                return total;

                List<int[]> MergeIntervals(List<int[]> lst)
                {
                    changed = false;
                    merged = false;
                    if (lst.Count != 1)
                    {
                        List<int[]> temp = new List<int[]>();
                        for (int i = 0; i < lst.Count - 1; i++)
                        {
                            int[] first = list[i];
                            int[] second = list[i + 1];


                            if (second[0] >= first[0] && second[1] <= first[1])
                            {
                                temp.Add(new int[] { first[0], first[1] });
                                changed = true;
                                merged = true;

                            }
                            if (second[0] >= first[0] && second[1] > first[1] && second[0] <= first[1])
                            {
                                temp.Add(new int[] { first[0], second[1] });
                                changed = true;
                                merged = true;
                            }
                            if (first[1] < second[0])
                            {
                                if (merged == true)
                                {
                                    temp.Add(new int[] { second[0], second[1] });
                                    merged = false;
                                }
                                else
                                {
                                    if (i == lst.Count - 2)
                                    {
                                        temp.Add(new int[] { first[0], first[1] });
                                        temp.Add(new int[] { second[0], second[1] });

                                    }
                                    else
                                    {
                                        temp.Add(new int[] { first[0], first[1] });
                                    }
                                }
                            }


                        }
                        return temp;
                    }
                    else return lst;

                }

                List<int[]> FindDistinct(List<int[]> lst)
                {

                    var dic = new Dictionary<string, int[]>();
                    foreach (var item in lst.OrderBy(l => l[0]))
                    {
                        string key = string.Join(",", item);
                        if (!dic.ContainsKey(key))
                        {
                            dic.Add(key, item);

                        }
                    }
                    return dic.Values.ToList();
                }
    }
}
```


## Did I Finish my Sudoku?
https://www.codewars.com/kata/53db96041f1a7d32dc0004d2


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

public class Sudoku
{
  public static string DoneOrNot(int[][] board)
  {
            for(int i = 0; i < board.Length; i++)
            {
                if (board[i].Distinct().ToArray().Length != 9) return "Try again!";               

                for(int j = 0; j < 9; j++)
                {
                    if (board[i][j] <= 0 && board[i][j] >= 10) return "Try again!";
                }
            }

            int[] columns = new int[9];
            for(int i = 0; i < 9; i++)
            {
                for(int j = 0; j < 9; j++)
                {
                    columns[j] = board[j][i];
                }
                if(columns.Sum() != 45) return "Try again!";              
            }

            int counter = 0;
            int[] regions = new int[9];
            for (int i = 0; i < 9; i+=3)
            {
                for (int j = 0; j < 9; j+=3)
                {
                   for(int k = 0;k < 3; k++)
                    {
                        for(int l = 0; l < 3; l++)
                        {
                            regions[counter] = board[k+i][l+j];
                            counter++;
                        }                      
                    }
                    if (regions.Distinct().ToArray().Length != 9) return "Try again!";
                    counter = 0;
                }
                
            }
            return "Finished!";
  }
}
```


## PaginationHelper
https://www.codewars.com/kata/515bb423de843ea99400000a

```csharp
using System;
using System.Collections.Generic;

public class PagnationHelper<T>
{
  int TotalItemsCount;
        int TotalPageCount;
        int ItemsPerPage;
        List<T> Collection = new List<T>();


        public PagnationHelper(IList<T> collection, int itemsPerPage)
        {
            TotalItemsCount = collection.Count;
            TotalPageCount = (Int32)Math.Ceiling((double)collection.Count / itemsPerPage);
            Collection = (List<T>)collection;
            ItemsPerPage = itemsPerPage;
        }

        
        public int ItemCount
        {
            get
            {
                return TotalItemsCount;
            }
        }

        
        public int PageCount
        {
            get
            {
                return TotalPageCount;
            }
        }

        
        public int PageItemCount(int pageIndex)
        {
            if (pageIndex >= 0 && pageIndex < TotalPageCount)
            {
                if(pageIndex < TotalPageCount - 1)
                {
                    return ItemsPerPage;                        
                }
                else
                {
                    return TotalItemsCount - ((TotalPageCount-1) * ItemsPerPage);
                }
            }
            else return -1;
        }

        
        public int PageIndex(int itemIndex)
        {
            int temp = -1;
            if (itemIndex >= 0 && itemIndex < TotalItemsCount)
            {
                for(int i = 0; i < TotalPageCount; i++)
                {
                    if(itemIndex >= ItemsPerPage * i)
                    {
                        temp++;
                    }
                }
                return temp;
            }
            else return -1;
        }
}
```
