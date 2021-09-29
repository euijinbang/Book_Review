# Meaningful Names

> 변수, 함수, 클래스의 이름은 모든 큰 질문에 답할 수 있어야 한다.
> 왜 존재하고, 무엇을 하는지, 어떻게 하는지를 말할 수 있어야 한다.
>



### Use Intention-Revealing Names

- We should choose a name that specifies what is being measured and the unit of that measurement

  무엇이 측정되고 있는지, 측정의 단위는 무엇인지 구체화할 수 있는 이름

- ```python
  # 좋은 예시
  int elapsedTimeInDays;
  int daysSinceCreation;
  int daysSinceModification;
  int fileAgeInDays;
  
  # 안좋은 예시 
  public List<int[]> getThem() {
   List<int[]> list1 = new ArrayList<int[]>();
   for (int[] x : theList)
    if (x[0] == 4)
    	list1.add(x);
   return list1;
   }
  
  # 좋은 예시
  public List<Cell> getFlaggedCells() {
   List<Cell> flaggedCells = new ArrayList<Cell>();
   for (Cell cell : gameBoard)
    if (cell.isFlagged())
     flaggedCells.add(cell);
   return flaggedCells;
   }
  ```

  

### Avoid Disinformation

- **Do not refer to a grouping of accounts as an 'accountList' unless it’s actually a List.**

- 리스트를 리스트라고 부르지 않는다..

  So 'accountGroup' or 'bunchOfAccounts' or just plain accounts would be better.

- Spelling similar concepts similarly is information.  비슷한 콘셉트는 스펠링도 비슷하게 써야 한다.

-  lower-case L or uppercase O 변수명으로 절대 쓰지 않는다.

  ```python
  int a = l;
  if ( O == l )
   a = O1;
  else
   l = 01;
  ```

  

### Make Meaningful Distinctions

- Number-series naming (a1, a2, .. aN) is noninformative;

- Noise words 사용하지 않는다

  - Info and Data
  - s
  - a, an, and the.

  

### Use Pronounceable Names

### Use Searchable Names

- Single-letter names and numeric constants have a particular problem in that they are not easy to locate across a body of text.

- single-letter names can ONLY be used as local variables inside short methods.

-  The length of a name should correspond to the size of its scope.

  - 이름의 길이는 범위와 비례해야 => 큰 범위를 다룰수록 구체적으로 검색하기 쉽게

  

### Avoid Encodings

### Interfaces and Implementations

- interface 와 implementation 중 하나를 선택한다면 후자, Imp



### Avoid Mental Mapping

- clarity is king!
- a loop counter 라도 i, j, k 는 되도록 피하자!



### Class Names

- Class와 ojects 이름은 명사 또는 명사구일 것

  ```python
  Good :) 
  Customer, WikiPage, Account, and AddressParser
  
  Bad :(
  Manager, Processor, Data, or Info
  ```

  

### Method Names

- Method 이름은 동사나 동사구일 것

  ```python
  Good :)
   postPayment, deletePage, or save.
    
  ```

  - Accessors, mutators, and predicates should be named for their value and prefixed with **get, set, and is**

    ```python
    string name = employee.getName();
    customer.setName("mike");
    if (paycheck.isPosted())...
    ```

  - When constructors are overloaded, use static factory methods with names that describe the arguments.

    ```python
    Complex fulcrumPoint = Complex.FromRealNumber(23.0);  => better!
    Complex fulcrumPoint = new Complex(23.0);
    ```



### Add Meaningful Context

- 접두사(prefixes) 활용 한 variables 이름 예시

```python
firstName, lastName, street, houseNumber, city, state, and zipcode
=> addrFirstName, addrLastName, addrState
```

- 모호한 콘텍스트 속 변수

```python
private void printGuessStatistics(char candidate, int count) {
 String number;
 String verb;
 String pluralModifier;
 if (count == 0) {
   number = "no";
   verb = "are";
   pluralModifier = "s";
 } else if (count == 1) {
   number = "1";
   verb = "is";
   pluralModifier = "";
 } else {
   number = Integer.toString(count);
   verb = "are";
   pluralModifier = "s";
 }
 String guessMessage = String.format(
 	"There %s %s %s%s", verb, number, candidate, pluralModifier
 );
 	print(guessMessage);
 }
```

- 명확한 콘텍스트

```python
public class GuessStatisticsMessage {
 private String number;
 private String verb;
 private String pluralModifier;
  
 public String make(char candidate, int count) {
	 createPluralDependentMessageParts(count);
	 return String.format(
    "There %s %s %s%s",
     verb, number, candidate, pluralModifier );
 }
  
 private void createPluralDependentMessageParts(int count) {
   if (count == 0) {
  	 thereAreNoLetters();
   } else if (count == 1) {
	   thereIsOneLetter();
   } else {
	   thereAreManyLetters(count);
   }
 }
  
 private void thereAreManyLetters(int count) {
   number = Integer.toString(count);
   verb = "are";
   pluralModifier = "s";
 }
  
 private void thereIsOneLetter() {
   number = "1";
   verb = "is";
   pluralModifier = "";
 }
  
 private void thereAreNoLetters() {
   number = "no";
   verb = "are";
   pluralModifier = "s";
 }
```

