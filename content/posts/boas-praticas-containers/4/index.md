# Container com acesso root


[Artigo Principal](../index.html/#4-permissões-excessivas-root-user)

O container que possuí acesso root, ou então roda com a flag `--privileged`, agrava e aumenta muito a superficie de ataque, permitindo escalar privilégios e dependendo do ataque habilitar mudanças no host através de um container com root e mapeamento de volumes. 

Para evitar o problema, procure usar imagens onde um USER é definido, de preferencia que só consiga executar a aplicação necessária. 

Exemplo de Dockerfile 

```
# Imagem base Python
FROM python:3.11-slim

# Criar um usuário não-root sem sudo
RUN useradd -m appuser

# Criar diretório da aplicação e mudar propriedade para appuser
WORKDIR /app
COPY app.py /app/

RUN chown -R appuser:appuser /app

# Mudar para o usuário appuser (não-root)
USER appuser

# Comando para rodar o app Python
CMD ["python", "app.py"]

```

Exemplo rodando esta imagem 

![rootless](./images/rootless.gif)

No Kubernetes também é possível rodar rootless, e afins, sempre opte por estas opções.

### AppArmor 

Através do [Apparmor](https://apparmor.net/), é possível definir profiles de segurança para execução de containers, como mostra neste tutorial da google.

[Artigo](https://cloud.google.com/container-optimized-os/docs/how-to/secure-apparmor?hl=pt-br)

Rodando com a opção `--security-opt` passamos um profile apparmor, que deve estar previamente instalado no root, tal qual sua policy também. 

```
docker run --rm -i --security-opt apparmor=no-ping debian:jessie bash -i
```

### Outros exemplos de uso 

Na doc do docker, eles comentam sobre o docker-default, que traz um certo nivel de segurança a chamadas do sistema e afins, e já é aplicado por default no docker. 

[Nesta doc](https://docs.docker.com/engine/security/apparmor/) é comentado sobre, e também possuem exemplos de outros profiles. 

