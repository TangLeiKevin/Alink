## Description
Latent Dirichlet Allocation (LDA), a topic model designed for text documents.

## Parameters
| Name | Description | Type | Required？ | Default Value |
| --- | --- | --- | --- | --- |
| selectedCol | Name of the selected column used for processing | String | ✓ |  |
| predictionCol | Column name of prediction. | String | ✓ |  |
| predictionDetailCol | Column name of prediction result, it will include detailed info. | String |  |  |
| reservedCols | Names of the columns to be retained in the output table | String[] |  | null |


## Script Example
#### Code
```python
data = np.array(["a b b c c c c c c e e f f f g h k k k", \
"a b b b d e e e h h k", \
"a b b b b c f f f f g g g g g g g g g i j j", \
"a a b d d d g g g g g i i j j j k k k k k k k k k", \
"a a a b c d d d d d d d d d e e e g g j k k k", \
"a a a a b b d d d e e e e f f f f f g h i j j j j", \
"a a b d d d g g g g g i i j j k k k k k k k k k", \
"a b c d d d d d d d d d e e f g g j k k k", \
"a a a a b b b b d d d e e e e f f g h h h", \
"a a b b b b b b b b c c e e e g g i i j j j j j j j k k", \
"a b c d d d d d d d d d f f g g j j j k k k", \
"a a a a b e e e e f f f f f g h h h j"])

df = pd.DataFrame({"doc" : data})
inOp = dataframe_to_operator(df, schema_str="doc string")


ldaTrain = LdaTrainBatchOp()\
            .setSelectedCol("doc")\
            .setTopicNum(6)\
            .setMethod("em")\
            .setSubsamplingRate(1.0)\
            .setOptimizeDocConcentration(True)\
            .setNumIter(50)

ldaPredict = LdaPredictBatchOp().setPredictionCol("pred").setSelectedCol("doc")

model = ldaTrain.linkFrom(inOp)

ldaPredict.linkFrom(model, inOp).collect_to_dataframe()
```

#### Results
##### Model

| model_id   | model_info |
| --- | --- |
| 0          | {"logPerplexity":"22.332946259667825","betaArray":"[0.2,0.2,0.2,0.2,0.2]","logLikelihood":"-915.6507966463809","method":"\"online\"","alphaArray":"[0.16926092344987234,0.17828690973899627,0.17282213771078062,0.18555794554097874,0.15898463316059516]","topicNum":"5","vocabularySize":"11"} |
| 1048576    | {"m":5,"n":11,"data":[6135.5227952852865,7454.918734235136,6569.887273287071,7647.590029783137,7459.37196542985,6689.783286316853,8396.842418256507,7771.120258275389,7497.94247894282,7983.617922597562,7975.470848777338,7114.413879475893,8420.381073064213,6747.377398176922,6959.728145538011,7368.902852508116,7635.5968635989275,6734.522904998126,6792.566021565353,6487.885790775943,8086.932892160501,8443.888239756887,7227.0417299467745,7561.023252667202,6264.97808011349,6964.080980387547,8234.247108608217,8263.190977757107,7872.088651923572,7725.669369347696,7591.453097717432,7733.627117746213,6595.2753568320295,8158.346230399092,7765.777648163369,6456.891859572009,6814.768507000475,6612.17371610521,6506.877213010642,7166.140342089344,7588.370517354863,7645.016947338933,8929.620632942893,6855.855247335312,7263.088264847597,7993.009126022237,7302.794183756114,6074.524636118613,6386.578740892538,8465.84700774072,7242.276290933901,7257.474039179472,7676.72445702261,6733.70550536632,7577.265607033211]} |
| 2097152    | {"f0":"d","f1":0.36772478012531734,"f2":0} |
| 3145728    | {"f0":"k","f1":0.36772478012531734,"f2":1} |
| 4194304    | {"f0":"g","f1":0.08004270767353636,"f2":2} |
| 5242880    | {"f0":"b","f1":0.0,"f2":3} |
| 6291456    | {"f0":"a","f1":0.0,"f2":4} |
| 7340032    | {"f0":"e","f1":0.36772478012531734,"f2":5} |
| 8388608    | {"f0":"j","f1":0.26236426446749106,"f2":6} |
| 9437184    | {"f0":"f","f1":0.4855078157817008,"f2":7} |
| 10485760   | {"f0":"c","f1":0.6190392084062235,"f2":8} |
| 11534336   | {"f0":"h","f1":0.7731898882334817,"f2":9} |
| 12582912   | {"f0":"i","f1":0.7731898882334817,"f2":10} |


##### Prediction

| doc        | pred       |
| --- | --- |
| a b b b d e e e h h k | 1          |
| a a b d d d g g g g g i i j j j k k k k k k k k k | 3          |
| a a a a b b d d d e e e e f f f f f g h i j j j j | 3          |
| a a b d d d g g g g g i i j j k k k k k k k k k | 1          |
| a a a a b b b b d d d e e e e f f g h h h | 3          |
| a b c d d d d d d d d d f f g g j j j k k k | 3          |
| a b b c c c c c c e e f f f g h k k k | 2          |
| a b b b b c f f f f g g g g g g g g g i j j | 0          |
| a a a b c d d d d d d d d d e e e g g j k k k | 3          |
| a b c d d d d d d d d d e e f g g j k k k | 3          |
| a a b b b b b b b b c c e e e g g i i j j j j j j j k k | 3          |
| a a a a b e e e e f f f f f g h h h j | 0          |

