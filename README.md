# desafio-dio-sistema-bancario
Desafio de projeto da plataforma [DIO](https://web.dio.me/) - Otimizando um sistema bancário em Python

## Objetivo:

Modificar o projeto: [Sistema bancário em Python](https://github.com/gtnasser/desafio-dio-sistema-bancario)

Separar as funções existentes de saque, depósito e extrato em funções. 

Criar novas funções: Cadastrar e Listar Clientes, Cadastrar e Listar Contas Bancárias.

## Requisitos:

### 1. Função Saque
* receber argumentos por nome (*keyword only*)
* sugestão parâmetros: saldo, valor, extrato, limite, num_saques, limite_saques
* sugestão retorno: saldo, extrato

### 2. Função Deposito

* receber argumentos por posição (*positional only*)
* sugestão argumentos: saldo, valor, extrato
* sugestão retorno: saldo, extrato

### 3. Função Extrato

* receber argumentos por posição (*positional only*) e nome (*keyword only*)
* argumentos posicionais: saldo, argumentos nomeados: extrato

### 4. Função Criar Conta-Corrente

* armazenar usuário em uma lista
* uma conta é composta por: agência, número da conta e usuário
* o número da agência é fixo (0001)
* o número da conta é sequqencial começando em 1
* o usuário pode ter mais de uma conta
* uma conta pertence a um único usuário

### 5. Função Criar Usuario

* armazenar usuário em uma lista
* atributos do usuário: nome, data de nascimento, cpf, endereço
* endereco = string com o formato "logradouro, nro, bairro, cidade/US
* CPF: armazenar comente números
* CPF: não permitir mais de um usuário com o mesmo CPF

### 6. Função Listar Conta-Corrente

* listar contas cadastradas

### 7. Função Listar Usuarios

* listar contas cadastradas

Código:

```python

# Desafio: Sistema Bancario
# - armazenar movimentacoes para mostrar no extrato
# - mostrar valores no formato R$ xxx.xx
# - mostrar mensagem de erro se valor do deposito nao for positivo 
# - mostrar mensagem de erro se ultrapassar qtd limite de saques
# - mostrar mensagem de erro se valor do saque ultrapassar limite por operacao
# - mostrar mensagem de erro no saque se nao houver fundos
# - mostrar saldo final no extrato

# Desafio: Otimizando Sistema Bancario
# - separar em funcoes
# - identificar mensagens de sucesso (i) ou de erro (X)
# - criar funcao novo usuario, validar se cpf nao esta cadastrado
# - criar funcao nova conta (agencia 0001, conta sequencial)
# - criar funcao listar contas
# - criar funcao listar usuarios

def main():
    saldo = 0.00
    extrato = []
    saques = 0
    SAQUES_MAX_QTD = 3
    SAQUES_MAX_VALOR = 500.00
    usuarios = []
    contas = []
    AGENCIA = "0001"

    def depositar(saldo, valor, extrato, /):
        if valor > 0:
            saldo += valor
            extrato.append(f"Deposito: R$ {valor:.2f}")
            print("(i) Depósito realizado com sucesso.")
        else:
            print("(X) Valor de depósito inválido.")
        return saldo, extrato

    def sacar(*, saldo, valor, extrato, saques, saques_valor):
        if  valor > saques_valor:
            print(f"(X) Valor excedeu o limite por saque.")
        elif valor > saldo:
            print("(X) Não será possível realizar esta operação por falta de saldo.")
        elif valor > 0:
            saldo -= valor
            extrato.append(f"Saque: R$ {valor:.2f}")
            saques += 1
            print("(i) Saque realizado com sucesso.")
        else:
            print("(X) Valor de saque inválido.")
        return saldo, extrato, saques

    def imprimir(saldo, /, *, extrato):
        print("\n" + " EXTRATO ".center(40, "="))
        if len(extrato)==0:
            print("Não foram realizadas movimentações.")
        else:
            for m in extrato:
                print(m)
            print(f"\nSaldo atual: R$ {saldo:3.2f}")
        print("=".center(40, "="), end="\n")

    def criar_usuario(usuarios):
        cpf = input("Informe o CPF (somente numeros): ")
        if buscar_usuario(int(cpf), usuarios):
            print("(X) CPF ja esta cadastrado.")
        else:
            nome = input("Informe o Nome completo: ")
            data_nascimento = input("Informe a Data de nascimento (dd-mm-yyyy): ")
            endereco = input("Informe o Endereco completo (logradouro, numero - bairro - cidade - UF): ")
            usuarios.append({"cpf": int(cpf), "nome": nome, "data_nascimento": data_nascimento, "endereco": endereco })
            print("(i) Usuario cadastrado com sucesso.")

    def buscar_usuario(cpf, usuarios):
        return [usuario for usuario in usuarios if usuario["cpf"]==cpf]

    def criar_conta(agencia, conta, contas, usuarios):
        cpf = input("Informe o CPF (somente numeros): ")
        usuario = buscar_usuario(int(cpf), usuarios)
        if usuario:
            contas.append({"agencia":agencia, "conta":conta, "usuario":usuario})
            print("(i) Conta cadastrada com sucesso.")
        else:
            print("(X) CPF nao esta cadastrado.")

    def listar_contas(contas):
        print("\n" + " CONTAS ".center(40, "="))
        for conta in contas:
            print(f"Agencia:{conta['agencia']} C/C:{conta['conta']} Titular:{conta['usuario']}'")
        print("=".center(40, "="), end="\n")

    def listar_usuarios(usuarios):
        print("\n" + " USUARIOS ".center(40, "="))
        for usuario in usuarios:
            print(f"CPF:{usuario['cpf']:<11d} Titular:{usuario['nome']}")
        print("=".center(40, "="), end="\n")

    opcao = -1
    while opcao!=0:
        opcao = int(input(
    """\n>> Opcoes: 
        1.Deposito 
        2.Saque 
        3.Extrato 
        4.Nova Conta 
        5.Novo Usuario 
        6.Listar Contas
        7.Listar Usuarios
        0.Sair
        >> """))

        if opcao==1:
            valor = float(input("Informe o valor do depósito: "))
            saldo, extrato = depositar(saldo, valor, extrato)

        elif opcao==2:
            print(f"saque: {saques+1}/{SAQUES_MAX_QTD}")
            if saques >= SAQUES_MAX_QTD:
                print(f"(X) Quantidade de saques excedeu o limite diário.")
            else:
                valor = float(input("Informe o valor do saque: "))
                saldo, extrato, saques = sacar(
                    saldo=saldo,
                    valor=valor,
                    extrato=extrato,
                    saques=saques,
                    saques_valor=SAQUES_MAX_VALOR
            )

        elif opcao==3:
            imprimir(saldo, extrato=extrato)

        elif opcao==4:
           conta = len(contas)+1
           criar_conta(AGENCIA, conta, contas, usuarios)

        elif opcao==5:
            criar_usuario(usuarios)

        elif opcao==6:
            listar_contas(contas)
            print(contas)

        elif opcao==7:
            listar_usuarios(usuarios)
            print(usuarios)

        elif opcao ==0:
            break

        else: 
            print("(X) Operação inválida, tente novamente")

main()

```
