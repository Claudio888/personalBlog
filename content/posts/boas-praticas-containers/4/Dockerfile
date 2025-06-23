# Imagem base oficial Python
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

