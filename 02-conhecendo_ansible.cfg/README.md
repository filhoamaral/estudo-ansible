O arquivo ansible.cfg é o centro de controle do Ansible. Ele define o comportamento global da ferramenta ao executar comandos ad-hoc ou playbooks, alterando desde o local do inventário até como as conexões SSH são estabelecidas. Se nenhum arquivo de configuração for localizado, as opções padrões serão aplicadas.

Também pode utilizar **ansible-config** para criar, modificar e/ou consultar o arquivo ansible.cfg

### Como o Ansible localiza o ansible.cfg (Ordem de Precedência)
Quando você executa qualquer comando (**ansible**, **ansible-playbook**), o Ansible procura o arquivo de configuração na seguinte ordem e usa o primeiro que encontrar:

1. **Variável de Ambiente:** $ANSIBLE_CONFIG (se definida no terminal).
2. **Diretório Atual:** ./ansible.cfg (no diretório onde você está executando o comando — opção recomendada para projetos e Git).
3. **Diretório Home do Usuário:** ~/.ansible.cfg (configuração pessoal do usuário).
4. **Padrão do Sistema:** /etc/ansible/ansible.cfg (configuração global padrão).

### Utilitário `ansible-config`
O utilitário *ansible-config* é a ferramenta de linha de comando oficial do Ansible criada para inspecionar, visualizar e validar a configuração ativa no seu ambiente.

Em vez de verificar manualmente a ordem de precedência dos arquivos ansible.cfg ou adivinhar qual pparâmetro está valendo, o ansible-config permite visualizar a configuração mesclada final de forma exata.

Sintaxe: `ansible-config`

#### Subcomandos principais
1. `ansible-config view`
   Exibe o conteúdo do arquivo ansible.cfg que está atualmente em uso pelo seu terminal (com base na ordem de precedência). Com um cat no arquivo
   ``` bash
   $ ansible-config view
2. `ansible-config dump`
   Mapeia e exibe todas as configurações ativas, juntamente com a origem de cada valor (se veio do arquivo .cfg, de uma variável de ambiente, do padrão ou da linha de comando). E diferencia entre cores da original e modificada
   ``` bash
   # Exibe todas as configurações ativas
   $ ansible-config dump

   # Exibe apenas as configurações que foram alteradas/customizadas (diferentes do padrão)
   $ ansible-config dump --only-changed
   
   $ansible-config dump --help
    usage: ansible-config dump [-h] [-v] [-c CONFIG_FILE] [-t {all,base,become,cache,callback,cliconf,connection,httpapi,inventory,lookup,netconf,shell,vars}] [--only-changed] [--format {json,yaml,display}] [args ...]

    positional arguments:
    args                  Specific plugin to target, requires type of plugin to be set

    options:
    -h, --help            show this help message and exit
    -v, --verbose         Causes Ansible to print more debug messages. Adding multiple -v will increase the verbosity, the builtin plugins currently evaluate up to -vvvvvv. A reasonable level to start is -vvv, connection debugging might require
                            -vvvv. This argument may be specified multiple times.
    -c, --config CONFIG_FILE
                            path to configuration file, defaults to first file found in precedence.
    -t, --type {all,base,become,cache,callback,cliconf,connection,httpapi,inventory,lookup,netconf,shell,vars}
                            Filter down to a specific plugin type.
    --only-changed, --changed-only
                            Only show configurations that have changed from the default
    --format, -f {json,yaml,display}
                            Output format for dump
3. `ansible-config list`
   Lista todas as opções de configuração suportadas pelo Ansible, mostrando o nome do parâmetro, descrição, valor padrão, tipo de dado e as variáveis de ambiente equivalentes.
   ``` bash
   $ ansible-config list --help
    usage: ansible-config list [-h] [-v] [-c CONFIG_FILE] [-t {all,base,become,cache,callback,cliconf,connection,httpapi,inventory,lookup,netconf,shell,vars}] [--format {json,yaml}] [args ...]

    positional arguments:
    args                  Specific plugin to target, requires type of plugin to be set

    options:
    -h, --help            show this help message and exit
    -v, --verbose         Causes Ansible to print more debug messages. Adding multiple -v will increase the verbosity, the builtin plugins currently evaluate up to -vvvvvv. A reasonable level to start is -vvv, connection debugging might require
                            -vvvv. This argument may be specified multiple times.
    -c, --config CONFIG_FILE
                            path to configuration file, defaults to first file found in precedence.
    -t, --type {all,base,become,cache,callback,cliconf,connection,httpapi,inventory,lookup,netconf,shell,vars}
                            Filter down to a specific plugin type.
    --format, -f {json,yaml}
                            Output format for list
4. `ansible-config init`
   Gera automaticamente um arquivo de configuração completo com todas as opções possíveis, descrições detalhadas e valores padrão comentados.
   1. Gerar uma configuração completa comentada
      Por padrão, o comando exibe na saída do terminal (stdout) um arquivo INI gigante com todas as opções e documentações embutidas
      ``` bash
      $ ansible-config init > ansible.cfg
   2. Gerar uma versão limpa (sem comentários)
      Se preferir um arquivo compacto, sem os blocos de texto explicativos, use a flag --disabled  
      ``` bash
      $ ansible-config init --disabled > ansible.cfg
   3. Formatos de Saída (--format)
      O ansible-config init aceita o pparâmetro --format para estruturar a configuração em diferentes linguagens
      ``` bash
      # INI (padrão)
      $ ansible-config init --format ini > ansible.cfg
      # YAML
      $ ansible-config init --format yaml > ansible.cfg.yml
      # JSON
      $ ansible-config init --format json > ansible.cfg.json
Mapeamento: Arquivo `ansible.cfg` X Variável de Ambiente (ENV)
O `ansible-config list` ajuda a enternder a equivalência entre a sintaxe do arquivo INI e as variáveis de ambiente no terminal
| Recurso          | Parâmento no ansible.cfg                                   | Variável de Ambiente (ENV)                                               |
| :--------------- | :--------------------------------------------------------- | :----------------------------------------------------------------------- |
| Inventário       | `inventory = ./hosts`       | ` export ANSIBLE_INVENTORY=./hosts`        |
| Checagem SSH     | `host_key_checking = False` | ` export ANSIBLE_HOST_KEY_CHECKING=False` |
| Formato de Saída | `stdout_callback = yaml`    | ` export ANSIBLE_STDOUT_CALLBACK=yaml`    |
| Usuário Remoto   | `remote_user = ubuntu`      | `export ANSIBLE_REMOTE_USER=ubuntu`       |

#### Exercício 1 - Criar um arquivo ansible.cfg
Criar um ansible.cfg somente com as configurações shell que posso alterar
Exemplo: ansible-config init -t shell > ansible.cfg

#### Modelo
Segue um arquivo como modelo (modelo_ansible.cfg) para ser salvo no /etc/ansible/ansible.cfg que ajuda modificar algum pontos de acordo com o ambiente