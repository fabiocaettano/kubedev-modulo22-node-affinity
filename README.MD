<h1>Gerenciamento de Distribuição de Pods</h1>

<h1>Node Affinity</h1>

O Node Affinity possui mais flexibilidade do que o Node Selector.

É possivel utilizar regras de obrigatoriedade ou regras de preferências.

O processo ocorre no momento do Schedule dos pods e não na Execução.

<h2>Configurar Label no Node</h2>

Visualizar os nodes: 

``` bash 
$ kubectl get nodes
```

Selecionar dois nodes e configurar a label:

``` bash
$ kubectl label node pool-uzfx4xaht-c70hk prioridade=1

$ kubectl label node pool-uzfx4xaht-c70hh prioridade=2
```

<h2>Testes</h2>

<h3>Primeiro Teste - Regra Obrigatória:</h3>

Neste teste os pods serão vinculados somente no node que possui a prioridade 1.

Os nodes excedentes não serão utilizados.

O total de réplicas dependerá da capacidade do node.

Trecho do manifesto:

``` yaml
affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: prioridade
                    operator: In
                    values:
                      - "1"
```

<h3>Segundo Teste - Regra Obrigatória:</h3>

Neste teste os pods serão vinculados no node que possui a prioridade 1 ou 2.

Os nodes excedentes não serão utilizados.

O total de réplicas dependerá da capacidade do node.

Trecho do manifesto:

``` yaml
affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: prioridade
                    operator: In
                    values:
                      - "1"
                      - "2"
```

<h3>Terceiro Teste - Regra Obrigatória:</h3>

Neste teste os pods serão vinculados no node que não possui a prioriade 1 ou 2.

Trecho do manifesto:

``` yaml
affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: prioridade
                    operator: DoesNotExist
```            

Para confirmar consulte o node que o pode está vinculado:

``` bash
$ kubectl get pods -o wide
```

<h3>Quarto Teste - Regra de Obrigadoriedade</h3>

Nestes teste os pods serão vinculados em todos os nodes que possui a label prioridade.

O total de réplicas dependerá da capacidade do node.

Trecho do manifesto:

``` yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
          - key: prioridade
            operator: Exists
```


<h3>Quinto Teste - Regra de Preferência</h3>

Neste casos os pods serão vinculados primeiro nos nodes que possuem a label de prioridade.

Depois disso os pods poderão ser vinculados ao node que não possui a label de prioridade.

``` yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
            - key: prioridade
              operator: In
              values:
                - "1"
                - "2"
```

<h3>Sexto Teste - Regra de Preferência  e Obrigatoriedade</h3>

Neste teste foi mesclado as duas regras.

Trecho do manisfesto:

``` yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: prioridade
              operator: Exists
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: prioridade
            operator: In
            values: 
              - "1"               
```



<h3>Retirar a label no node:</h3>

``` bash
$ kubectl label node pool-uzfx4xaht-c70hk database-
``` 