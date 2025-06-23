# Ataques via `COPY` 



[Artigo Principal](../index.html/#8-ataques-via-copy-de-arquivos-maliciosos)


Muito similar ao problema com o entrypoint, ele também possui a solução ou mitigação parecida. 

### O problema

Quando usamos o `COPY` nossa intenção é copiar arquivos da raiz de onde o Dockerfile esta sendo construido, e joga-lo para dentro do container. 

Fazemos isto com nossas aplicações, seja em arquivo para posterior build, ou binários ja construidos. 

O problema principal é utilizar o `COPY .` que copia toda a raiz para dentro do container, piorando desempenho pois muitas vezes temos arquivos grandes em grandes aplicações, e também copiando coisas que não sabemos o que é. 

Um atacante pode deixar um script de backdor, ou substituir o arquivo de `entrypoint.sh` e nem percebemos, portanto é importante saber o conteudo que esta jogando para dentro do container. 

Muitas vezes podemos jogar uma chave `.pem` ou um certificado importante, que posteriormente podem ser acessados por ataque a layers ou ao proprio container. Variaveis de ambiente também e afins. 

### Mitigação 

Saber o conteudo que esta indo para dentro do container. 

Entender o que é realmente necessário para a aplicação rodar, e tentar ser o mais enxuto possível nesta hora. 

E conhecer o conteudo do seu repo, conhecer o que são os arquivos da aplicação e afins. 

Use o `.dockerignore`, `.gitignore`, não copie chaves de segurança, use variaveis de ambiente em tempo de execução e não de build. 

