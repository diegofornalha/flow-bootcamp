# 

Uma das grandes coisas que o playground Flow não pode fazer é implantar contratos no testnet. Hora de se formar no playground para um ambiente de desenvolvimento adequado.

### 🤠Instale a extensão do código Cadence VS

Primeiro, [baixar e instalar o VS Code](https://code.visualstudio.com/download) se você ainda não o tem. Vamos precisar da extensão Cadence, que está oficialmente disponível para o Código VS e não oficialmente para o [Plataforma IntelliJ](https://github.com/cadence-tools/cadence-for-intellij-platform#installation).

Os usuários do MacOS / Linux podem instalar a extensão do mercado:

[https://camo.githubusercontent.com/e2273787fa21c0fc5b6ee451afe6b2ee454fcef781d9d4c0dce4a79578597a30/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f48794a4b4a49644b352e706e67](https://camo.githubusercontent.com/e2273787fa21c0fc5b6ee451afe6b2ee454fcef781d9d4c0dce4a79578597a30/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f48794a4b4a49644b352e706e67)

**USUÁRIOS DE JANELAS!** Devido a problemas com a versão mais recente ( 0.7.0 ) da extensão no Windows, você precisará instalar o Windows Subsystem for Linux e executar todo esse projeto no WSL. Isso é bem fácil! Saída [as instruções oficiais](https://docs.microsoft.com/en-us/windows/wsl/install) e siga a instalação completa. Ao longo deste projeto, sempre que digo "em um terminal", você precisará abrir um terminal dentro da WSL2! Isso é mais fácil se você instalar o WSL remoto `https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl` extensão que permitirá que você trabalhe remotamente no Linux.

Aqui está um vídeo que fiz explicando esse processo:

[Loom](https://www.loom.com/share/39909f64123a400ebd113d8382a0c726)

Se você não conseguir configurar o WSL em sua máquina, precisará instalar manualmente uma versão mais antiga da extensão Cadence.

Inicie o terminal e execute estes comandos:

`git clone -b submissions/issue-3/koala https://github.com/onflow/vscode-cadence.git
cd vscode-candence
npm i
npm run package
code --install-extension cadence-0.6.4.vsix`

Isso fará o download da versão mais antiga, navegará em sua pasta, instalará as dependências, construirá e instalará no VS Code. Você também precisará desativar as atualizações automáticas para que o VSCode não as atualize, basta abrir as configurações ( CTRL +, ) e procurar a atualização automática da extensão e garantir que ela esteja definida como nenhuma!

[https://camo.githubusercontent.com/3ead06117910350499e7f443a096c280939718dc55b67486bd75efa5b1f5e64d/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f724a393430487559392e706e67](https://camo.githubusercontent.com/3ead06117910350499e7f443a096c280939718dc55b67486bd75efa5b1f5e64d/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f724a393430487559392e706e67)

Observe que, enquanto a versão mais antiga é executada, ela tem problemas com o emulador. Eu recomendo a instalação do WSL!

### 🤠Instale a extensão Cadence para a plataforma IntelliJ

Como mencionado, a extensão não é oficialmente suportada no IntelliJ. Também faltam alguns recursos ( no momento da redação ), então deixarei que você decida se deseja usá-lo. Eu recomendo apenas ficar com o código VS. Você pode encontrar a versão IntelliJ [aqui](https://github.com/cadence-tools/cadence-for-intellij-platform#installation).

![https://user-images.githubusercontent.com/231274/175892621-ac4e8764-36b5-4f4a-b16d-2a214bd0ee0a.png](https://user-images.githubusercontent.com/231274/175892621-ac4e8764-36b5-4f4a-b16d-2a214bd0ee0a.png)

### 🎮Instale a CLI de fluxo

Em seguida, instalaremos a CLI Flow. Isso é fácil!

**Usuários Mac** Abra o seu terminal e injete este Homebrew.

`brew install flow-cli`

**Usuários Linux e WSL** Execute isso no seu terminal:

`sh -ci "$(curl -fsSL https://storage.googleapis.com/flow-cli/install.sh)"`

Se `flow version` não é reconhecido depois disso, corra `export PATH=$PATH:/home/YOUR_MACHINE_NAME_HERE/.local/bin`

**Windows** Se você instalou o WSL, execute o comando Linux acima de ^.

Se você quiser tentar a sorte no Windows nativo, abra o PowerShell e execute:

`iex "& { $(irm 'https://storage.googleapis.com/flow-cli/install.ps1') }"`

É isso aí! Você pode verificar sua instalação executando

`flow version`

Aqui está o que vejo no Windows:

[https://camo.githubusercontent.com/cda7aba9c9b64b9f8c010bd833c2f790b682f33decfb2eca365f3c73b4edf721/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f723132637766554f392e706e67](https://camo.githubusercontent.com/cda7aba9c9b64b9f8c010bd833c2f790b682f33decfb2eca365f3c73b4edf721/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f723132637766554f392e706e67)

E no MacOS:

[https://camo.githubusercontent.com/eda9eed9c903c9f25ff7a1de04480b80281af6d2599f8dc8296598cd8af89da2/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f53796879694b5f4b352e706e67](https://camo.githubusercontent.com/eda9eed9c903c9f25ff7a1de04480b80281af6d2599f8dc8296598cd8af89da2/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f53796879694b5f4b352e706e67)

Sua versão pode ser diferente, tudo bem!

A CLI Flow nos permite fazer um monte de coisas legais. Podemos usá-lo para conversar com a rede Flow, implantar contratos e até enviar transações. Vamos usá-lo para configurar um projeto Flow!

### 🐣Configure um projeto Flow

Tudo bem, estamos prontos para começar. Crie um `Learn-Flow` pasta em algum lugar da sua máquina. Esta pasta conterá todos os nossos arquivos de projeto. Vou manter o meu na área de trabalho.

`# Desktop/
mkdir Learn-Flow
cd Learn-Flow`

Agora vamos criar uma pasta para o nosso contrato inteligente e inicializar um projeto de fluxo nele

`# Desktop/Learn-Flow
mkdir FlowNFTs
cd FlowNFTs
flow init`

[https://camo.githubusercontent.com/e2896bcf81956e9c9d8a25430cd56adc896d7b94ed4162c6ab7bc71b32f9b238/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f426b643848494f74712e706e67](https://camo.githubusercontent.com/e2896bcf81956e9c9d8a25430cd56adc896d7b94ed4162c6ab7bc71b32f9b238/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f426b643848494f74712e706e67)

Isso criará um `flow.json` arquivo que possui detalhes básicos de configuração para o blockchain Flow. Você pode ler mais sobre isso em [a página de configuração aqui](https://docs.onflow.org/flow-cli/configuration/).

Trabalharemos muito com esse arquivo para que você aprenda tudo sobre isso em breve!

🚨**EDITOR RESTART AQUI**🚨 É uma boa ideia reiniciar o editor VSCode aqui, para que a extensão Cadence instalada detecte que você está prestes a gravar fluxo e entre em ação!

Você pode abrir o `FlowNFTs` pasta no código VS inserindo `code .` no terminal. Se isso não fizer nada, você não instalou o comando shell na sua máquina. Você pode instalá-lo iniciando a paleta de comandos (`CMD/CTRL`+`SHIFT`+`P`) e digitando "comando shell".

[https://camo.githubusercontent.com/86e7e3f19119d1364da818c9c7ae8d399b3140bc3abf821aeeca1229f55d1bb5/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f42794c734e386459712e706e67](https://camo.githubusercontent.com/86e7e3f19119d1364da818c9c7ae8d399b3140bc3abf821aeeca1229f55d1bb5/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f42794c734e386459712e706e67)

### 🔥Implante um contrato localmente

Tão rápida recapitulação, temos nossa CLI FLOR instalada, inicializamos nosso projeto FLOR e configuramos nossa conta de testnet. Feijão fresco. Vamos agora escrever um contrato simples e implantá-lo localmente.

No seu `FlowNFTs` pasta, crie uma `contracts` diretório e faça um arquivo de contrato NFT. Estou chamando o meu `BottomShot.cdc` ( o oposto do TopShot lol ), você pode chamá-lo como quiser.

Abra este arquivo no código VS e adicione este contrato NFT para bebês:

`pub contract BottomShot {

    // Declare an NFT resource type
    pub resource NFT {
        // The ID that differentiates each NFT
        pub let id: UInt64

        // String mapping to hold metadata
        pub var metadata: {String: String}

        // Initialize both fields in the init function
        init(initID: UInt64) {
            self.id = initID
            self.metadata = {}
        }
    }

    // Create a single new NFT and save it to account storage
    init() {
        self.account.save<@NFT>(<-create NFT(initID: 1), to: /storage/BottomShot1)
    }
}`

Este contrato é bastante direto. Permite criar NFTs individuais com apenas dois atributos: o ID e a sequência de metadados.

`self.account.save<@NFT>(<-create NFT(initID: 1), to: /storage/BottomShot1)`

É aqui que criamos uma NFT. Não estamos verificando IDs, portanto é possível criar várias NFTs com o mesmo ID, o que é bom por enquanto. Não estamos definindo a sequência de metadados, então esta é uma NFT haha muito inútil

Repartição da sintaxe:

- `self.account.save`
    
    : Ligue para a função de salvamento na conta que implantou este contrato
    
- `<@NFT>`
    
    : Estamos salvando um recurso do tipo NFT
    
- `<-`
    
    : Mover um recurso ( não pode usar "=" )
    
- `create NFT(initID: 1)`
    
    : Criando uma NFT, passando no argumento id 1
    
- `to: /storage/BottomShot1`
    
    : O segundo argumento que a função save leva - onde queremos salvá-lo
    

Em seguida, faremos algo bastante mágico: vamos criar uma blockchain!

A CLI Flow vem com algo chamado Emulador de Fluxo - uma ferramenta leve que emula o comportamento da rede Flow real. Basicamente, cria uma blockchain de fluxo local em sua máquina. Você pode iniciá-lo executando isso no seu terminal

`flow emulator`

Como isso está emulando uma rede real de blockchain, você precisará manter o terminal em execução para manter o blockchain vivo.

**NOTA - QUESTÕES DE EXTENSÃO** A extensão Cadence às vezes não detecta que você iniciou seu emulador. Se você vir uma mensagem "O emulador está offline" no início do arquivo do contrato, feche a janela do terminal executando o emulador e clique na mensagem do seu arquivo.

[https://camo.githubusercontent.com/8166263ff049897499879b172e467b93adccfe922091c5a46b4db91a06e2956b/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f53796c6c68446a6446632e706e67](https://camo.githubusercontent.com/8166263ff049897499879b172e467b93adccfe922091c5a46b4db91a06e2956b/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f53796c6c68446a6446632e706e67)

Isso iniciará o terminal em sua própria janela e o anexará à extensão.

[https://camo.githubusercontent.com/99d1cdc342e200847c6665d233be889d9441cd41df73e79b4dddab194bae6600/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f533135754f6f6459632e706e67](https://camo.githubusercontent.com/99d1cdc342e200847c6665d233be889d9441cd41df73e79b4dddab194bae6600/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f533135754f6f6459632e706e67)

Vamos implantar um contrato nesta blockchain local. Crie uma nova janela de terminal e execute o seguinte:

`flow accounts add-contract BottomShot ./contracts/BottomShot.cdc`

A sintaxe aqui é

`flow accounts add-contract <name> <filename>`

Isso adicionará o contrato ao nosso emulador local, eis o que meu terminal mostra:

[https://camo.githubusercontent.com/670e88dc6dc509e0db2a15293e0b1dd049fec2b2bc2397b011fdad34fe78ff05/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f726b2d326e716459392e706e67](https://camo.githubusercontent.com/670e88dc6dc509e0db2a15293e0b1dd049fec2b2bc2397b011fdad34fe78ff05/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f726b2d326e716459392e706e67)

Enquanto o terminal que estiver com meu emulador estiver em execução, este contrato estará acessível lá.

**NOTA - QUESTÕES DE EXTENSÃO!** Novamente, a extensão às vezes não consegue detectar que você acabou de implantar um contrato. Se isso acontecer com você, reinicie seu editor e **somente** use os prompts de extensão! Não use os comandos!

[https://camo.githubusercontent.com/5713e4fcd3736e09a72c68f31f03064b5a03c1d14b8a96c5c35c562a9206fe25/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f486b446a466f4f46352e706e67](https://camo.githubusercontent.com/5713e4fcd3736e09a72c68f31f03064b5a03c1d14b8a96c5c35c562a9206fe25/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f486b446a466f4f46352e706e67)

Bem feito! É assim que é fácil desenvolver localmente no Flow. Agora estamos prontos para percorrer todo o caminho com um contrato NFT adequado, não apenas um que faça NFTs em branco.

### 🚨Relatório de andamento

Olá, escritor de contratos inteligentes Flow.

Poste uma captura de tela da saída do terminal mostrando o contrato implantado localmente!