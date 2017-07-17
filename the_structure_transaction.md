# The structure of transaction

Transactions are the most important part of the Bitcoin protocol , The other parts of the Bitcoin protocol are also around the generation , broadcasting , verification and packaging of transcations .
The content of this paper is mainly for the transaction data structure and analysis of the raw transaction , Later we will continue to write `the life cycle of the transaction` and `transaction script` and other articles .
## Raw Transcation
The transcations of the bitcoin is stored in the block by bytes , use the bitcoin-cli command to get an raw transcation .  E.g:
>bitcoin-cli getrawtransaction 2eb0e06af852f049f7dc641f740ded17a11cde138fd3d3d3c4a078649c053260

We will get a complete raw transaction :
```
010000000112255d3c...88ac00000000[^rawtransaction]
```
The raw transcation shown here is treated by `hex` and does not show the original byte stream . From the abvoe `hex` string and can not see useful information , json fromat of the transcation can view [api](https://chain.api.btc.com/v3/tx/2eb0e06af852f049f7dc641f740ded17a11cde138fd3d3d3c4a078649c053260?verbose=3)
The raw transcation data can not intuitively see the specific content of the transxation , with the example of this transxation cane be a detailed analysis of the transation structure and the original data analysis .
## The strcuct of transaction
A complete transxation consists of the following elements :
* version
* transaction input
* transaction output
* lock_time

the input and output of the transcation many occur one or more , The above transaction has an input and two output components . `Version` is a clear reference  to the rules of a transcation , unless theer is a major upgrade in the case , the version number basically no change , is a relatively fixed value .
And the lock time of the transcation is the earliest transcation time that the transcation was added to the block , and in most cases his value is 0, indicating that it needs to be added immediately to the block . If the lock time is greater than 0 and less than 500 million , it represends the block height . If more than 500 million , it means a Unix timestamp .
What is the composition of the transcation described above and how is it reflected in the raw transcation? It would need to dismantle the original transcation and analyze how the structure of the transcation was defined in the original transcation.

 Description |Size | Raw Data
------|----   |----
version  |4       | 01000000
 tx_in_count  |1+     | 01
tx_in |41+  |  12255......ffffffff [^ins]
tx_out_count  |1+    |  02
tx_out  |9+    |  00e1f...988ac[^outs]
lock_time  |4      |  00000000

Through the above table you can see how an raw transcation is stored in the transcation , In the original transcation , in addition to the data required for the transcation , since the input and the output of the transcation may be more than one , the original transcation also requires additional fields to describe the transcation's input ,the number of outputs , the number of inputs , and the number of inputs or the number of outputs is not fixed , so the number used to describe the number is to use variable length(VarInt) , his purpose is to save space in the case can be stored enough to use the integer.
Here is a description of the transcation data structure and how he stored in the original transcation , but did not analyze the specific input,output content . The folloeing input and output analysis ,start form the input. 
## Transcation In
The input of a transcsation can consist of the following elements :
* previous_hash
* previous_index
* signature script

So how does the transcation input store in the original transcation? From the above analysis we can know that there is only one transcation in the transcation . The following is the original transcation from the analysis of the transcation under the `input`:

 Description | Size |Raw Data
------|----   |----
previous_hash  |32       | 12255...ae[^previous_hash]
previout_inde  |4     |  01000000
script length | 1+  | 8a
signature script|？    |  4730...eae[^in_script]
sequence | 4    | ffffffff

Here to get the hash and the site above the hash and show the same, this is because the byte order storage and read the way inconsistent , It is [Endianness](https://en.wikipedia.org/wiki/Endianness)
> The network protocol specifies that the first byte is received in the high byte and is stored at a low address , so the first byte is sent to the lower byte of the data

In the bitmap storage hash is stored as an integer , so in the hash when the need to start from the low address .
And the length of the unlock script is unkonwn , you need to use a variable shaping to indicate the lendth of the unlock script . The analysis of the script will be analyzed in a later article . Through the above table can be described from a transcation to analyze his input ,the following look at the output of the analysis .

## Transaction Out

The output of a transcation consists of the following elements :
* value
* script

How does the transcation's output store in the original transcation? from the above transcation analysis can know that the example transcation is two output , where only need to be resolved for the first output can be.

Descriptoon | Size | Raw Data
------|----   |----
value  |8       | 00e1f50500000000
script length  |1+     | 19
script |？  | 76a9147072795a259b38bf476e053852ab85221ba9467b88ac

Note that the amount of output also involves the issue of large-end transcation , when the need to resolve from the low address began to read.
There is no analysis of the lock script , it can not see the output address ,for a bit currency transcations , the transcation itself is not concerned about the output of the address ,the transcation only need to care about the lock script , when someone can use the right to use The unlock script can be.

The data structure of the transcation itself do4s not have the concept of `transcation fee` , and the transcation fee for each transcation is calculated using the `total input - total output`.


[^rawtransaction]:010000000112255d3cd1e5a59bec64057b0d2b2a7f3c9a9e1f14d0f1b362b72e96743d69ae010000008a473044022065d352a27ed3039e7fbca5315c38b5d255e68e9919964906c5dfe3cfea7abe11022070036614521710506873b769ff8bb53dc7350f752fc687ed483713eca136b611014104d5d461083771ac542a6417a8424b74ba56d47f77e888cde408a508189d88bcef9bbb7292b750774da227dbd326db2a2efbeaab9789e57b946a41ab895c0d2eaeffffffff0200e1f505000000001976a9147072795a259b38bf476e053852ab85221ba9467b88acc0570100000000001976a9140cb6c275be7f179883bb821ef1dfd6b520fc656988ac00000000
[^ins]:12255d3cd1e5a59bec64057b0d2b2a7f3c9a9e1f14d0f1b362b72e96743d69ae010000008a473044022065d352a27ed3039e7fbca5315c38b5d255e68e9919964906c5dfe3cfea7abe11022070036614521710506873b769ff8bb53dc7350f752fc687ed483713eca136b611014104d5d461083771ac542a6417a8424b74ba56d47f77e888cde408a508189d88bcef9bbb7292b750774da227dbd326db2a2efbeaab9789e57b946a41ab895c0d2eaeffffffff
[^outs]:00e1f505000000001976a9147072795a259b38bf476e053852ab85221ba9467b88acc0570100000000001976a9140cb6c275be7f179883bb821ef1dfd6b520fc656988ac
[^previous_hash]:12255d3cd1e5a59bec64057b0d2b2a7f3c9a9e1f14d0f1b362b72e96743d69ae
[^in_script]:473044022065d352a27ed3039e7fbca5315c38b5d255e68e9919964906c5dfe3cfea7abe11022070036614521710506873b769ff8bb53dc7350f752fc687ed483713eca136b611014104d5d461083771ac542a6417a8424b74ba56d47f77e888cde408a508189d88bcef9bbb7292b750774da227dbd326db2a2efbeaab9789e57b946a41ab895c0d2eae

