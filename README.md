# Tutorial de Instalação do Magento

Após dedicar várias horas de pesquisa e explorar diversos tutoriais, finalmente encontrei uma solução eficaz. Agora, estou compartilhando este tutorial passo a passo para ajudar você a instalar o Magento em seu ambiente de desenvolvimento, levando em consideração todos os pré-requisitos necessários. Siga as instruções cuidadosamente e aproveite a configuração do Magento de forma bem-sucedida.

## Passo 1: Configuração do Ambiente

* 1.1. Certifique-se de que você tenha o Xampp instalado em seu sistema. Baixe a versão que seja compatível com o PHP 7.3 ou superior.
* 1.2. Instale o Xampp seguindo as instruções do instalador.
* 1.3. Após a instalação do Xampp, abra o painel de controle do Xampp e inicie o Apache e o MySQL.

## Passo 2: Instalação do Composer

* 2.1. Baixe e instale o Composer seguindo as instruções da documentação oficial do Composer.

## Passo 3: Instalação do ElasticSearch

* 3.1. Baixe a versão 7.0 do Elasticsearch, pois o Magento ainda não suporta a versão 8.
* 3.2. Navegue até a pasta "htdocs" dentro do diretório do Xampp em seu sistema.
* 3.3. Descompacte a pasta do Elasticsearch dentro da pasta "htdocs".
* 3.4. Acesse a pasta "elasticsearch > bin" dentro da pasta do Elasticsearch.
* 3.5. Execute o arquivo "elasticsearch.bat" para iniciar o serviço do Elasticsearch.
* 3.6. Confirme se o serviço foi iniciado acessando `http://localhost:9200` em seu navegador. Você deve ver um JSON com alguns dados do serviço.

## Passo 4: Criação do Banco de Dados

* 4.1. Acesse `http://localhost/phpmyadmin/` em seu navegador.
* 4.2. Crie um novo banco de dados com um nome de sua escolha. Por exemplo, você pode usar "magento_db".

## Passo 5: Habilitando as extensões

* 5.1. No Xampp, o arquivo `php.ini` geralmente está localizado na pasta php do diretório de instalação. Você pode seguir o caminho: `C:\xampp\php\php.ini.` 
* 5.2. Abra o arquivo php.ini em um editor de texto e procure pela seção [Extensions] no arquivo para descomentar. Se não existir, você pode adicioná-la no final do arquivo.
```py
    extension=intl
    ...
    extension=soap
    extension=sockets
    extension=sodium
    ...
    extension=xsl
```
* 5.3. Salve as alterações e reinicie o servidor Apache no painel de controle do Xampp para que as alterações tenham efeito.

## Passo 6: Instalação do Magento

* 6.1. Abra um terminal ou prompt de comando e navegue até a pasta raiz do Xampp (normalmente, algo como "C:\xampp\htdocs" no Windows).
* 6.2. Execute o seguinte comando para baixar o Magento usando o Composer:
```py
    composer create-project --repository=https://repo.magento.com/ magento/project-community-edition
```
com versão
```py
    composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.4.5
```
* 6.3. Durante o processo de instalação, você será solicitado a fornecer suas credenciais da conta do Magento Marketplace. Certifique-se de ter uma conta criada e as credenciais em mãos.

## Passo 7: Modificando arquivos

**Observação: Sempre verifique cuidadosamente as aspas simples e duplas, pois é possível copiá-las incorretamente devido à plataforma utilizada.**

* 7.1. Abra a pasta do Magento em seu editor de escolha, navegue até a pasta `vendor/magento/framework/Image/Adapter` e abra o arquivo `Gd2.php`. 
* 7.2. Localize a função `validateURLScheme` no arquivo e substitua todo o conteúdo com o seguinte código:
```py
private function validateURLScheme(string $filename) : bool
{
   $allowed_schemes = ['ftp', 'ftps', 'http', 'https'];
   $url = parse_url($filename);
   if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename)) 
    {
       return false;
     }
   return true; 
}
```    
* 7.3. Salve o arquivo Gd2.php. 
* 7.4. Navegue até a pasta `vendor/magento/framework/View/Element/Template/File` e abra o arquivo `Validator.php`.
* 7.5. Modifique a linha que contém `$realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path));` para:
```py
    $realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path));
```
* 7.6. Para finalizar, siga até pasta do magento e acesse `vendor\magento\framework\Interception`.
* 7.7. Abra o arquivo `PluginListGenerator.php` procure por $cacheId para substituí-lo:
```py
    $cacheId = implode('-', $this->scopePriorityScheme) . '-' . $this->cacheId;
```
## Passo 8: Executando Comandos do Magento no CMD

* 8.1. Abra o CMD na pasta do Magento.
* 8.2. Este comando realiza a instalação do Magento e configura várias opções, incluindo a URL base, banco de dados, informações do administrador, idioma, moeda, fuso horário e configurações do Elasticsearch. Certifique-se de que as opções do comando estejam corretas para o seu ambiente: 
```py
    php bin/magento setup:install --base-url=http://127.0.0.1:8082 --db-host=localhost --db-name=magento2 --db-user=root --db-password="" --admin-firstname=Magento --admin-lastname=User --admin-email=user@example.com --admin-user=admin --admin-password=admin123 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --search-engine=elasticsearch7 --elasticsearch-host=http://localhost --elasticsearch-port=9200
 ```
* 8.3. Após executar o comando, aguarde o processo de instalação ser concluído.
* 8.4. Durante a instalação, você poderá ver um nome de acesso ao painel de administração gerado automaticamente, como "admin_wrr7yw". Anote esse nome de acesso, pois ele será necessário para acessar o painel de administração do Magento posteriormente.
* 8.5. Após a conclusão da instalação, siga executando os seguintes comandos separadamente:

```py
    php bin/magento setup:di:compile
```
* Este comando compila o código do Magento e gera arquivos de dependência. É importante executá-lo sempre que você fizer alterações no código do Magento.
```py
    php bin/magento indexer:reindex
```
* Este comando reindexa os índices do Magento. É necessário executá-lo sempre que houver alterações nos dados do Magento, como produtos, categorias ou atributos.
```py
    php bin/magento setup:upgrade
```
* Este comando atualiza o esquema do banco de dados do Magento para refletir quaisquer alterações feitas nos módulos. É importante executá-lo sempre que você fizer atualizações ou instalações de novos módulos.
```py
    php bin/magento setup:static-content:deploy -f en_US en_GB
```
* Este comando implanta os arquivos estáticos do Magento para os idiomas en_US (Inglês dos EUA) e en_GB (Inglês do Reino Unido). Certifique-se de substituir esses idiomas pelos que você está usando no seu projeto.
```py
    php bin/magento deploy:mode:set developer
```
* Este comando define o modo de implantação do Magento como "developer" (desenvolvedor). No modo de desenvolvedor, o Magento exibe mensagens detalhadas de erro e regenera automaticamente os arquivos estáticos. É recomendado para ambientes de desenvolvimento.
```py
    php bin/magento cache:clean
```
* Este comando limpa o cache do Magento, removendo os arquivos em cache e os dados armazenados em cache. É útil executá-lo após fazer alterações no código ou na configuração do Magento.
```py
    php bin/magento cache:flush
```
* Este comando descarrega completamente o cache do Magento, removendo todos os arquivos e dados em cache. Use com cuidado, pois isso pode levar algum tempo para regenerar o cache quando o Magento for acessado novamente.
```py
    php bin/magento module:disable Magento_Csp
```
* Este comando desativa o módulo Magento_Csp. Você pode substituir "Magento_Csp" pelo nome do módulo que deseja desativar. Lembre-se de que a desativação de um módulo pode afetar a funcionalidade do Magento, portanto, faça isso apenas se necessário.
```py
    php bin/magento module:disable Magento_TwoFactorAuth
```
* Este comando desativa o módulo Magento_TwoFactorAuth, que é responsável pela autenticação de dois fatores no Magento. Se você não precisa dessa funcionalidade de segurança adicional, pode desativá-la usando esse comando.

## Passo 9: Finalização

Para prosseguir, retorne ao phpMyAdmin e siga estas etapas:
* 9.1. Abra o phpMyAdmin e selecione o banco.
* 9.2. Vá para a seção "SQL" na parte superior da página.
* 9.3. Na área de texto fornecida, cole a seguinte query:
```py
INSERT INTO `core_config_data`(`path`, `value`) VALUES ('dev/static/sign', 0) ON DUPLICATE KEY UPDATE `value`=0
```
* 9.4. Para executar a query e aplicar as alterações, clique no botão "Executar" no phpMyAdmin.
* 9.5. Em seguida, execute o seguinte comando para limpar o cache exclusivamente das configurações:
```py
php bin/magento cache:clean config
```
Isso garantirá que as alterações de configuração sejam atualizadas corretamente.

Por fim, para iniciar a aplicação Magento localmente, use o seguinte comando:
```py
php -S 127.0.0.1:8082 -t ./pub/ ./phpserver/router.php
```
Após a conclusão do comando, você deverá ver o painel do Magento. 

![magento](https://github.com/tamirespatrocinio/MagentoTutorial/assets/73259410/a89e62ae-3968-467f-b5e6-a41c6a525d7e)

Para acessar o painel de administração, basta abrir a URL no seu navegador:
`http://127.0.0.1:8082/admin_wrr7yw/`.
Certifique-se de substituir "admin_wrr7yw" pela URL correta do seu painel de administração.


## Erro!
* Se ocorrer um bug semelhante ao mostrado na imagem, siga os comandos abaixo para solucioná-lo e rode a aplicação novamente: 

![erro](https://github.com/tamirespatrocinio/MagentoTutorial/assets/73259410/8a3be36f-127d-46d1-97ab-3ed909519c2d)
```py
    php bin/magento setup:upgrade
```
```py
    php bin/magento setup:static-content:deploy -f 
```
* Este comando é utilizado para implantar os arquivos estáticos do Magento. A opção -f indica que você deseja forçar a implantação, substituindo os arquivos estáticos existentes, se houver. Ao executar este comando, o Magento irá compilar e copiar os arquivos estáticos relevantes para os idiomas configurados no seu projeto. Isso inclui arquivos CSS, JavaScript, imagens e outros recursos estáticos necessários para o funcionamento correto da loja virtual.

