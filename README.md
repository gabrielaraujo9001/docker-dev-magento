# Ambiente para desenvolvimento de pacotes Magento 2

Esse repositório contém um ambiente de desenvolvimento com instaladores automaticos e comandos pré-definidos para facilitar o desenvolvimento de pacotes dentro do Magento 2.

Para esse SDK funcionar é necessário que você tenha o [Docker](https://docs.docker.com/docker-for-mac/install/) e [Docker Compose](https://docs.docker.com/compose/install/#install-compose) instalado. Caso você esteja usando OSX é necessário o [Docker Sync](https://docker-sync.readthedocs.io/en/latest/getting-started/installation.html).


Uma vez que o repositório foi clonado e instalado você terá um ambiente com:

* PHP 7.2
* Nginx
* Maria DB
* Redis
* NPM
* NVM
* Grunt
* Composer
* XDebug
* PHPMyAdmin
* Mailhog

## Instalação

```bash
$ PROJETO="magento-pacotes-bleez" && git clone git@github.com:gabrielaraujo9001/docker-dev-magento.git $PROJETO && cd $PROJETO
```

Você pode trocar `magento-pacotes-bleez` por qualquer outro nome de sua preferência.

Em seguida instale o ambiente rodando:

```bash
$ ./install
```

> O Comando `./install` também irá clonar o [Magento Bleez](https://github.com/Bleez/magento-dev-pacotes) próprio para desenvolvimento de pacotes. Ele contém comandos especiais para ajudar no desenvolvimento e testes dos pacotes. Consulte a página do repositório para saber mais.

> Dica: Caso queira trabalhar com outro Magento no lugar do *Magento Bleez* você pode criar manuamente a apsta `src` e colocar seu Magento dentro. Faça isso ANTES de rodar o comando `./install`.

### Iniciando containers

Depois que esse processo terminar você precisa iniciar os containers para começar a usar o ambiente com o comando `./start`.

Exemplo:
```bash
$ ./start
```

> Por padrão o ambiente usará as portas 80, 8080 e 3306. Se quiser mudar as portas que os containers usa edite arquivo `.env` localizado na raiz.

Este comando instalará todas as imagens que o Docker precisa para fazer o ambiente funcionar e iniciará os mesmos.

Se você quiser parar os containers execute o comando `./stop`.

Se você estiver usando OSX o docker ficará rodando no seu terminal quando executar `./start`. Nesse caso para parar os containers execute `CTRL + C`.

Exemplo:
```bash
$ ./stop
```

### Instalando o Magento

Para instalar o Magento precisaremos entrar dentro do container e executar a instalação lá de dentro. Para isso, uma vez que você iniciou os containers com o comando `./start`, digite:

```bash
$ ./shell php
```

Uma vez dentro do container rode o comando `composer install` para instalar as dependências.

```bash
$ composer install
```

Nesse momento o composer pedirá para você informar suas chaves de autenticação. Você pode conseguí-las seguinto [estas instruções](https://devdocs.magento.com/guides/v2.3/install-gde/prereq/connect-auth.html).

> :warning: Dependendo do pacote que você estiver usando pode ser solicitado a chave OAuth do Github. [Siga essas instruções para pegá-la](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line).

> :warning: Guarde suas chaves em um local fácil, pois se você precisar executar `./update` no ambiente tudo será resetado e você precisará informar suas chaves novamente.

Depois que o `composer install` terminar execute `install-magento` para fazer a instalação do Magento.

```bash
$ install-magento
```

> Dica: Se você precisar reinstalar o Magento do zero basta executar novamente `install-magento` que uma nova instalação limpa será feita

Uma vez instalado a loja estará disponível no endereço `http://127.0.0.1/`. Para acessar a administração use as credenciais:

* **Url:** http://127.0.0.1/admin
* **Login:** admin
* **Senha:** admin1234

> Dica: Se você não quiser uma instalação com os valores padrões você pode acessar`http://127.0.0.1` e fazer sua própria instalação do Magento

## Banco de dados

Você pode acessar o banco de dados com as seguintes configurações:

* **Host:** 127.0.0.1
* **Porta:** 3306
* **Login:** magento
* **Senha:** magento

> Por padrão a senha de root é **123**

Você também pode utilizar o phpMyAdmin acessando `http://127.0.0.1:8080`.

> Dica: Você pode alterar a senha de root e as portas do banco e phpMyAdmin alterando o `.env`

## Entrando nos containers

Pode acontecer casos que você precise entrar dentro de um dos containers. Caso você tenha essa necessidade você pode fazer isso usando o comando `./shell <servico>`.

Por exemplo, caso você queira executar no container que roda o PHP, execute:

```bash
$ ./shell php
```

Estes são os serviços disponíveis nesse comando:

* php
* nginx
* db
* phpmyadmin

## Personalizando seu docker-compose.yml

Como o `docker-compose.yml` do ambiente é compilado e as vezes é necessário recompilá-lo, existe o arquivo `docker-compose.custom.yml` onde você pode colocar suas próprias definições do Docker Compose.

Esse arquivo é apenas criado no momento da instalação e nunca mais é alterado pelo ambiente.

## Lista de comandos disponíveis

Abaixo a lista de todos os comandos disponíveis no ambiente.

### Comandos para gerenciar ambiente

Os comandos abaixo devem ser executados na rais do ambiente


| Comando  | Descriçao  | Opçoes & Exemplos |
|---|---|---|
| `./start` | Inicia os containers que farão o ambiente funcionar.      | |
| `./stop`  | Desliga os containers sem destruí-los.        | |
| `./kill` | Desliga e destrói todos os containers, volumes, networks e imagens usadas. Os arquivos do ambiente e do magento dentro de `/src` **não são excluídos**.                     |  |
| `./logs`  | Mostra na tela o logs de execução de todos os containers. Ideal para entender se algum container teve problema para iniciar.       | |
| `./shell`  | Entra dentro do container de um determinado serviço.        | `./shell php`|
| `./update`  | Atualiza ambiente com as últimas modificações. ||

> O comando ./update não exclui o conteúdo da pasta /src mas refaz todos os containers, portanto tudo que tiver no banco de dados será perdido e o Magento deve ser instalado novamente.


#### Exemplo:

```bash
$ ./start
```



### Comandos para trabalhar com Magento

Os comando abaixo devem ser executados dentro do container `php` através do comando `./shell php`.

| Comando  | Descriçao  | Opçoes & Exemplos |
|---|---|---|
| `install-magento` | Executa instalação do Magento que está instalado na dentro de `/src`. Se você executar este comando com um Magento já instalado tudo será apagado e uma nova instalação será feita.||      
| `redis-flush`  | Limpa todo o Redis, tanto o cache de arquivos como a sessão.        | |
| `xdebug`  | Habilita ou desabilita o uso do Xdebug ao rodar o PHP via linha de comando.       | |
| `m2_upgrade`  | Limpa a pasta `/var` e roda o `setup:upgrade`        | |
| `m2_clear`  | Limpa a pasta `/var` e apaga todos os arquivos compilados pelo Magento (`/generated` e `/pub/static`).        | |
| `m2_deploy`  | Limpa tudo rodando `m2_clear` e em seguida roda `setup:static-content:deploy pt_BR -f`.        | |
| `m2_reindex`  | Reindexa a loja inteira       | |
| `m2_collect_phrases`  | Executa `i18n:collect-phrases` dentro da pasta do tema do cliente (`/app/project/tema-do-cliente`) especificado por parâmetro.        | |
| `disable_fucking_modules`  | Desabilita modulos que não são necessários para desenvolvimento. Isso pode deixar o processamente e a velocidade do Magento mais leve.        | |



#### Exemplo
```bash
$ install-magento
```

