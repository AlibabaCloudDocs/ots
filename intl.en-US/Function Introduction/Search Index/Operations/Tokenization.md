# Tokenization

After a tokenization method is set for TEXT fields, Tablestore tokenizes values into multiple words based on the specified tokenization method. Tokenization cannot be set for fields of non-TEXT types.

You can use the match query and match phrase query to query TEXT data. Term query, terms query, prefix query, and wildcard query are also used in a few scenarios.

## Tokenization methods

The following tokenization methods are supported:

-   Single-word tokenization \(SingleWord\)

    This method applies to all natural languages such as Chinese, English, and Japanese. By default, the tokenization method for TEXT fields is single-word tokenization.

    After single-word tokenization is set, Tablestore performs tokenization based on the following rules:

    -   Chinese texts are tokenized based on each character.
    -   English letters or digits are tokenized based on spaces or punctuation marks. Uppercase letters are converted to lowercase letters. For example, "Hang Zhou" is tokenized into "hang" and "zhou". You can use match query or match phrase query and set the keyword to "hang", "HANG", or "Hang" to query the rows that contain "Hang Zhou".
    -   Alphanumeric characters such as the model number are also separated by spaces or punctuation marks. However, these characters cannot be tokenized into smaller words. For example, “IPhone6" can be tokenized into “IPhone6". When you use match query or match phrase query, you must specify “iphone6". No results are returned if you use "iphone".
    The following table describes the parameters for single-word tokenization.

    |Parameter|Description|
    |---------|-----------|
    |caseSensitive|Specifies whether this method is case-sensitive. The default value is false. false indicates that all English letters are converted to lowercase letters.If you do not need Tablestore to convert English letters to lowercase letters, you can set the caseSensitive parameter to true. |
    |delimitWord|Specifies whether to tokenize alphanumeric characters. The default value is false.You can set the delimitWord parameter to true to separate English letters from digits. This way, "iPhone6" is tokenized into "iPhone" and "6". |

-   Delimiter tokenization \(Split\)

    Tablestore provides general dictionary-based tokenization. However, special industries need to use tokenization for custom dictionaries. To address this need, Tablestore provides delimiter tokenization. You can perform tokenization by using custom methods, use delimiter tokenization, and write the data to Tablestore.

    Delimiter tokenization applies to all natural languages such as Chinese, English, and Japanese.

    After the tokenization method is set to delimiter tokenization, the system tokenizes field values based on the specified delimiter. For example, a field value is "badminton,ping pong,rap". The delimiter is set to a comma \(,\). The value is tokenized into "badminton", "ping pong", and "rap". The field is indexed. When you use match query or match phrase query to query "badminton", "ping pong", "rap", or "badminton, ping pong", the row can be obtained.

    The following table describes the parameters for delimiter tokenization.

    |Parameter|Description|
    |---------|-----------|
    |delimiter|The delimiter. By default, the value is a whitespace character.    -   When you create a search index, the delimiter set for the field for tokenization must be the same as that in the written data. Otherwise, data may not be retrieved.
    -   When the custom delimiter is set to a number sign \(\#\), replace the delimiter with escape characters. |

-   Minimum semantic unit-based tokenization \(MinWord\)

    This method applies to Chinese in full-text search scenarios.

    After the tokenization method is set to minimum semantic unit-based tokenization, Tablestore tokenizes the TEXT field values into minimum semantic units when Tablestore performs a query.

-   Maximum semantic unit-based tokenization \(MaxWord\)

    This method applies to Chinese in full-text search scenarios.

    After the tokenization method is set to maximum semantic unit-based tokenization, Tablestore tokenizes the TEXT field values into the maximum number of semantic units when Tablestore performs a query. However, different semantic units may overlap. The total length of the tokenized words is greater than the length of the original text. The index fields are increased.

    This method can generate more tokens and increase the probability of obtaining query results. However, the index fields are greatly increased. Match phrase query also tokenizes words in the same way, which causes tokens to overlap and data to possibly not be retrieved. Therefore, this tokenization method is more suitable for match query.

-   Fuzzy tokenization

    This method applies to all natural languages such as Chinese, English, and Japanese in scenarios where the text content is small, such as titles, movie titles, and book titles.

    This method has minimal delay when results are returned, but the index fields are greatly increased. Therefore, this tokenization method is suitable for short text.

    After the tokenization method is set to fuzzy tokenization, Tablestore performs tokenization by using n-gram. The results are between minChars and maxChars. For example, this method is used to populate the drop-down list.

    -   Limits
        -   You can use fuzzy tokenization to tokenize only the TEXT field values that are smaller than 256 characters in length. If the length limit is exceeded, Tablestore truncates the exceeded characters and discards them. Only the first 256 characters are retained.
        -   To prevent excessive expansion of index data, the difference between the values of maxChars and minChars must not exceed 6.
    -   Parameters

        |Parameter|Description|
        |---------|-----------|
        |minChars|The minimum number of characters for a token. The default value is 1. We recommend that you set this value to 2.|
        |maxChars|The maximum number of characters for a token. The default value is 3.|


## Comparison

The following table compares the previous tokenization methods in different dimensions.

|Item|Single-word tokenization|Delimiter tokenization|Minimum semantic unit-based tokenization|Maximum semantic unit-based tokenization|Fuzzy tokenization|
|----|------------------------|----------------------|----------------------------------------|----------------------------------------|------------------|
|Index expansion|Medium|Small|Small|Large|Huge|
|Relevance|Weak|Weak|Medium|Relatively strong|Relatively strong|
|Applicable language|All|All|Chinese|Chinese|All|
|Size limit|None|None|None|None|32|
|Recall rate|High|Low|Low|Medium|Medium|

