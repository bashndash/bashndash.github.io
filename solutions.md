# Java Problems and Solutions
a collection of samples

## Table of Contents
- [Convert an integer into binary representation](#convert-an-integer-into-binary-representation)
- [Read a text file line by line using NIO](#read-a-text-file-line-by-line-using-niohttpsdocsoraclecomenjavajavase18corejavaniohtml))
- [Create SHA-256 checksum of a file](#create-sha-256-Checksum-of-a-file)

## Convert an integer into binary representation
```java
int number = 21;
String binaryRepresentation = Integer.toBinaryString( number );
```

## Read a text file line by line using [NIO](https://docs.oracle.com/en/java/javase/18/core/java-nio.html)
```java
Path path = Paths.get( "C:/log/sample.txt" );
try
{
  Files.lines( path ).forEach( System.out::println );
}
catch( IOException e )
{
  e.printStackTrace();
}
```

## Create SHA-256 Checksum of a file
```java

  public static void main(String[] args)
  {
    Path path = Paths.get( "C:/log/sample.txt" );
    dumpChecksum( path );
  }

  private static void dumpChecksum( Path path )
  {
    try( InputStream is = Files.newInputStream( path, StandardOpenOption.READ ) )
    {
      MessageDigest shaDigest = MessageDigest.getInstance( "SHA-256" );

      //byte array to read data in chunks
      byte[] byteArray = new byte[1024];
      int bytesCount = 0;

      //read data and update digest until file ends
      while( (bytesCount = is.read( byteArray ) ) != -1 )
      {
        shaDigest.update(byteArray, 0, bytesCount);
      };

      // get digest as byte array
      byte[] digest = shaDigest.digest();

      StringBuilder sb = new StringBuilder();
      for( byte b : digest )
      {
        // turn (signed) byte into unsigned byte (0-255)
        int unsignedInt = Byte.toUnsignedInt( b );

        // convert to a string representing a hex number ("0"-"ff")
        String hexString = Integer.toHexString( unsignedInt );

        // append leading zero if necessary
        if( unsignedInt < 10 )
        {
          sb.append( "0" );
        }

        // convert signed integer to hex (00-FF)
        sb.append( hexString.toUpperCase( Locale.ROOT ) );
      }

      System.out.println( sb.toString() );
    }
    catch( IOException e )
    {
      e.printStackTrace();
    }
    catch( NoSuchAlgorithmException e )
    {
      e.printStackTrace();
    }
  }
```


