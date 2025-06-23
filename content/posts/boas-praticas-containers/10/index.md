# Builds não reproduziveis

[Artigo Principal](../index.html/#10-builds-não-reproduzíveis)



Quando não setamos direito versões de softwares, dependencias e afins, estamos sujeitos ao que vier. 

### O problema

Ao construir uma imagem, ela ser analisada por scanners, e posteriormente ser construida novamente. Vamos imaginar que nosso Dockerfile e aplicação não deinfa versões para nada, ao construir uma vez, estamos com apenas 4 vulnerabilidades LOW, porém durante o processo de build, devido ao maior azar da historia, uma vulnerabilidade critica e altamente letal foi adicionada a versão `latest` da imagem base que estamos usando, com isto agora no mesmo processo de build, ao construir novamente a imagem, não temos somente 4 CVEs LOW, temos também a 1 CRITICAL, que da por exemplo acesso ao shell por atacantes remotos. 
Isto é improvavel de acontecer, porém não é impossivel, num mundo onde atualizações de software estão cada vez mais frequentes, podemos sofrer alguma mudança em tempo de build e integrar um erro que passe despercebido pelos nossos sitemas de alerta. 

Numa proxima build, um `trivy`, `sonar`, entre outros, poderiam pegar a falha, e impedir que o software fosse a produção ficar exposto a este risco. Aqui estamos falando de um problema de tempo, um azar gigantesco, mas que pode ocorrer em outros momentos caso definir versões e gerencia-las não for uma preocupação do time. 

### Mitigação


Quando estamos rodando o Dockerfile em uma pipeline, tentamos usar o conceito de construa uma vez e use muitas, ou seja, cada build gerado de um PR, deve ser unico e construido apenas uma vez. 

Isto garante que o resultado de scanners de segurança, de assinatura de SBOM e afins faça esta valido até esta aplicação ir para produção. 

O uso do framework SLSA pode ajudar nesta prevenção gerando relatórios da construção da imagem. 

Estar em comum acordo com times de desenvolvimento para definir padronizações nas aplicações, garantir boas práticas para uma imagem base, são principios que ajudam a mitigar problemas como este, de ter imagens mal otimizadas e por muitas vezes inseguras. 


