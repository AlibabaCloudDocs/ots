# Installation

This topic describes how to install Tablestore SDK for Java.

## Prerequisites

To install Tablestore SDK for Java, you must use JDK 6.0 or later versions.

## Install Tablestore SDK for Java

You can install Tablestore SDK for Java by using one of the two following methods:

-   Add dependencies to a Maven project

    To use Tablestore SDK for Java in Maven, you need only to add the corresponding dependencies to the pom.xml file. In this example, Tablestore SDK for Java V5.4.0 is used. Add the following content to <dependencies\>:

    ```
    <dependency>
        <groupId>com.aliyun.openservices</groupId>
        <artifactId>tablestore</artifactId>
        <version>5.4.0</version>
    </dependency>                 
    ```

-   Import JAR packages into Eclipse

    In this example, Tablestore SDK for Java V5.4.0 is used. To import JAR packages into Eclipse, perform the following operations:

    1.  [Download the Tablestore SDK for Java package](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/java/tablestore-5.4.0-release.zip).
    2.  Decompress the SDK for Java package.
    3.  Copy the tablestore-<versionId\>.jar file and all files in the lib folder to your project.

        <versionId\> specifies the version of Tablestore SDK for Java. For example, the file name for V5.4.0 is tablestore-5.4.0.jar.

    4.  Right-click your project name in Eclipse and choose **Properties** \> **Java Build Path** \> **Add JARs**.
    5.  Select all JAR packages you copied in Step 3.

## Sample programs

Tablestore SDK for Java provides a variety of sample programs for your reference or use. You can decompress the downloaded SDK package and view the sample programs in the examples folder.

