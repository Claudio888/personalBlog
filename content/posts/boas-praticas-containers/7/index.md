# Dependencias comprometidas


[Artigo Principal](../index.html/#7-dependências-comprometidas-supply-chain)


Quando criamos imagens mais completas e para uso de produção, muitas aplicações em python, javascript, java, baixam inumeras dependencias e bibliotecas que ajudam o software a desempenhar corretamente suas funções.

### O Problema

O que nos ajuda a garantir que todas as bibliotecas são confiaveis, estão em versões estaveis é definir versões a elas. 

Quando temos versões não definidas, ou latest, estamos sujeitos a coletar as ultimas vulnerabilidades logo de cara, e também nos adicionando a dificuldade do debug, pois não sabemos que versão estamos usando e afins. 

Outro problema que pode ocorrer, é não gerenciarmos nossas imagens base, e usarmos a de fornecedores, mesmo que famosos, estamos sujeitos a suas modificaçoes, e possiveis comprometimentos da cadeia de suprimentos, como instalação de software malicioso e afins. 

### Como mitigar 

Hardcodar a versão ou usar hashes especificas para versões e fix especificos podem ajudar a mitigar o problema. 

Construir pipelines de gestão de imanges base, apesar de complexo e custoso, garante que você e suas aplicações só usem imagens confiaveis e testadas para vulnerabilidades anteriormente. 

Conceitos de SBOM, que visa gerar um relatório e dizer o que esta no software, e SLSA que visa garantir que a imagem não foi comprometida durante sua construção, podem trazer maior segurança e garantir confiabilidade em imagens. 

### Exemplo SBOM 

Usando a ferramenta [syft](https://github.com/anchore/syft) podemos gerar o SBOM, e com [grype](https://github.com/anchore/grype) analisar vulnerabilidades e assinaturas (feitas geralmente com cosign) em nossas imagens. 

Com o comando abaixo consigo retirar um relatório de SBOM de uma imagem criada no step 6, e posteriormente analisar seus pacotes e verificar as vulnerabildades presentes 

```
syft exemplocerto -o spdx-json > exemplocerto-sbom.spdx-json

grype sbom:exemplocerto-sbom.json 

```

![sbom](./images/sbom.gif)

Neste exemplo estamos olhando o arquivo sbom, e procurando em bancos de dados de CVEs se os pacotes e versões estão ou não comprometidos. 

Com o SBOM, temos uma impressão, do que nossa imagem tem naquele tempo, podendo haver comparações após atualizações ou inserções de novas imagens. 

### SLSA

Para SLSA não conseguirei dar um exemplo muito completo, pois precisaria gerar um arquivo de confiabilidade em json, que rodaria numa pipeline, este arquivo iria dizer todos os passo que aquela imagem teve. 

O SLSA, garante que numa possível pipeline de imagens base, ou pipelines de implantação de software, nossa imagem foi construida daquela forma e não foi mais modificada, por isto é importante usar o conceito de `build once use many` para construirmos a imagem no inicio do processo e usa-la a mesma até estar em produção, os controles de framework SLSA garantem isto. 

[Mais informações SLSA](https://github.com/slsa-framework)
