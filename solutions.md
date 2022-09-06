# Java Problems and Solutions
a collection of samples

## Convert an integer into binary representation
```java
int number = 21;
String binaryRepresentation = Integer.toBinaryString( number );
```

## Read a text file line by line using [NIO]
```
Path path = Paths.get( "C:/log/sample.txt" );
try
{
  Files.lines(path).forEach( System.out::println );
}
catch( IOException e )
{
  e.printStackTrace();
}
```

