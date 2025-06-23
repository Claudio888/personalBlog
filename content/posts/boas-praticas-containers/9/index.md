# Container scape e privilégios elevados. 
---


[Artigo Principal](../index.html/#9-container-escape)


Como já conversamos nesta serie de documentos, privilégios excessivos causam problemas excessivos. 

### O problema do --privileged

Nunca rode o container com este comando, ele garante também acesso root ao host, o que facilita imensamente a vida de um atacante. 

Ele provavelmente vai conseguir editar, remover, e acessar qualquer arquivo no host, ou seja na maquina que esta rodando aquele container. 

Imagine você roda um workdpress depreciado (ou o latest mesmo), e roda isso numa VM na GCP com docker e o container como `--privileged` pois precisava debugar algumas coisas e o wordpress é chato de configurar em container. 

Se um atacante encontra uma brecha no workdpress, ganha acesso ao container, ele imediatamente vai ter acesso ao host também, e se sua instancia tiver por exemplo uma key `.json` de uma service account admin, que você criou só porque como admin é mais rapido de criar esta VM, pronto, a receita pra destruição e caos total esta feita, uma escalada de falhas, que permite praticamente a destruição da nuvem. 

Claro que é um caso extremo, mas mostra como coisas pequenas, eventos encadeados mesmo que aparentemente pequenos, podem causar danos irreversiveis. 

Geralmente as aplicações não precisam de todos estes privilegios para rodar, então nunca use o --privileged, nem para testes nem para nada. 

### Docker in Docker (DinD)

Este é quando rodamos docker dentro de outro docker, pois é, é possivel, porem para que ele consiga funcionar geralmente exige um mapeamento ao `/var/lib/docker` do Docker host. 

Exemplo de comando que o atacante pode fazer 

```
# Dentro do container que esta dentro do container, um atacante pode fazer:
docker run -v /:/hostfs --privileged alpine chroot /hostfs
```

Qualquer comando provavelmente poderá ser executado no host do docker também. No Jenkins isto é muito comum, as vezes em outras ferramentas de pielines também, porém não é o recomendado. 

Um cenário tipico, em que a ferramenta [kaniko](https://github.com/GoogleContainerTools/kaniko) salva, é quando precisamos fazer build de uma imagem dentro de um container, que geralmente é um runner do github actions, ou gitlab CI.

O kaniko permite fazer build e construir containers a partir de um Dockerfile, de dentro de outro container. 


