# Installation

This topic describes how to install Tablestore SDK for Python.

## Prerequisites

Python 2 or Python 3 is available.

## Install Tablestore SDK for Python

-   Method 1: Use pip.

    Run the following command to install Tablestore SDK for Python:

    ```
    sudo pip install tablestore
    ```

-   Method 2: Use GitHub.

    Make sure that you have installed Git. For more information about how to download Git, visit [Git](https://git-scm.com/downloads). After Git is installed, run the following command:

    ```
    git clone https://github.com/aliyun/aliyun-tablestore-python-sdk.git
    sudo python setup.py  install              
    ```

-   Method 3: Use source code.
    1.  Download the [SDK package](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/python/aliyun-tablestore-python-sdk-5.1.0.tar.gz).
    2.  Decompress the SDK package. Then, run the following command:

        ```
        sudo python setup.py install       
        ```


## Verify Tablestore SDK for Python

To check the SDK version in the Python environment, enter python on the command line and press Enter.

```
>>> import tablestore
>>> tablestore.__version__
'5.2.1'       
```

## Uninstall Tablestore SDK for Python

Run the following command to uninstall Tablestore SDK for Python by using pip:

```
sudo pip uninstall tablestore     
```

