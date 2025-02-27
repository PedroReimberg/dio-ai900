# Lab 1 Machine Learning na pratica

## **Links Importantes**
[Explore Azure AI Services](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/02-content-safety.html)
[Explore Automated Machine Learning in Azure Machine Learning](https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/01-machine-learning.html)

## Passo a passo 

### Criação do recurso

- Acesse o portal da Azure e, na barra de pesquisa superior, procure por "Azure Machine Learning" e selecione o recurso.
- Dentro do recurso, selecione **Create** e, em seguida, **New Woskspace** para entrar na tela de criação.
- Crie o recurso selecionando uma assinatura, um grupo de recursos (crie um novo, caso necessário), um nome para a Workspace e uma região.
- Todas as outras configurações não serão alteradas para este laboratório.
- Clique no botão **Review + create** e aguarde a criação do recurso.
- Após o recurso ser criado, clique em **Go to resource**.

### Machine Learning Studio

- Após abrir o recurso, é possível acessar o Azure Machine Learning Studio pelo botão **Launch Studio**.
- No Machine Learning Studio, clique em **Automate ML** no menu à esquerda.
- Crie um novo trabalho com as seguintes configurações:
    - **Basic settings**:
        - **Job name**: Mantenha o nome preenchido automaticamente.
        - **New experiment name**: `mslearn-bike-rental`
        - **Description**: Automated machine learning for bike rental prediction.
        - **Tags**: Vazio
    - **Task type & data**:
        - **Select task type**: Regression
        - **Select dataset**: Crie um novo dataset com estas configurações:
            - **Data type**:
                - **Name**: `bike-rentals`
                - **Description**: `Historic bike rental data`
                - **Type**: Table (mltable)
            - **Data source**:
                - Selecione **From local files**
            - **Destination storage type**:
                - **Datastore type**: Azure Blob Storage
                - **Name**: workspaceblobstore
            - **MLtable selection**:
                - **Upload folder**: Baixe e descompacte a pasta com 2 arquivos para upload em: `https://aka.ms/bike-rentals`
- Depois de enviar os arquivos, crie o dataset.
- Selecione o dataset criado utilizando o task type: `Regression`
- Crie a tarefa com as seguintes configurações:
    - **Task settings**:
        
        - **Task type**: Regression
        - **Dataset**: bike-rentals
        - **Target column**: rentals (integer)
        - **Additional configuration settings**:
            - **Primary metric**: NormalizedRootMeanSquaredError
            - **Explain best model**: Desmarcado
            - **Enable ensemble stacking**: Desmarcado
            - **Use all supported models**: Desmarcado
            - **Allowed models**: Selecione apenas **RandomForest** and **LightGBM.**
        - **Limits** (Expanda esse menu):
            - **Max trials**: `3`
            - **Max concurrent trials**: `3`
            - **Max nodes**: `3`
            - **Metric score threshold**: `0.085`
            - **Experiment timeout**: `15`
            - **Iteration timeout**: `15`
            - **Enable early termination**: Marcado
        - **Validation and test**:
            - **Validation type**: Train-validation split
            - **Percentage of validation data**: 10
            - **Test dataset**: None
        
        **Compute**:
        - **Select compute type**: Serverless
        - **Virtual machine type**: CPU
        - **Virtual machine tier**: Dedicated
        - **Virtual machine size**: Standard_DS3_V2... (Altere de acordo com sua assinatura)
        - **Number of instances**: 1
- Envie o treino e aguarde o trabalho ser concluído.

### Avalie o melhor modelo

- Quando o status alterar para **Completed**, clique no nome em azul, abaixo de **Algorithm name**, para acessar a página de detalhes do melhor modelo.
- Na aba de métricas, selecione **residuals** and **predicted_true** caso não estejam selecionados.
- Nesta interface, é possível visualizar a performance do modelo.

### Criando uma implantação do modelo

- Selecione a aba **Model** e, em seguida, clique em **Deploy** no modelo, utilizando a opção **Real-time endpoint** com as seguintes configurações:
    - **Virtual machine**: Standard_DS3_v2...(Altere de acordo com sua assinatura)
    - **Instance count**: 3
    - **Endpoint**: New
    - **Endpoint name**: Não altere
    - **Deployment name**: Não altere
    - **Inferencing data collection**: Desabilitado
    - **Package Model**: Desabilitado
- Aguarde até receber a mensagem `Success: Model deployment is successfully triggered`.

### Testando o modelo

- No menu à esquerda do Machine Learning Studio, selecione **Endpoints** e abra o endpoint criado.
- Dentro do endpoint, clique na aba **Test**.
- Nesta aba, é possível testar o modelo. Altere o template JSON para o seguinte texto:
    
```
{ 
	"input_data": {
		"columns": [
			"day",
			"mnth",
			"year",
			"season",
			"holiday",
			"weekday",
			"workingday",
			"weathersit",
			"temp",
			"atemp",
			"hum",
			"windspeed"
		],
			"index": [0],
			"data": [[1,1,2022,2,0,1,1,2,0.3,0.3,0.3,0.3]] 
	 }
}
```
    
- Clique em **Test**.
- O resultado deve ser um número próximo a 351.

### Limpeza

- Lembre-se de apagar todos os recursos criados para não gerar custos adicionais à sua assinatura.