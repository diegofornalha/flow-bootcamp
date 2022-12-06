# 

Como estamos usando FCL, podemos usar *qualquer* Carteira de fluxo que queremos! Vou usar a carteira Flow mais popular - [Bocto](https://docs.onflow.org/flow-token/available-wallets/#how-to-use-blocto).

Você também pode experimentar [Lilico](https://lilico.app/) - é uma carteira de custódia que funciona como uma extensão do navegador, assim como outras carteiras populares. Se você decidir usar isso, faça o download e configure-o agora.

Volte para o código VS e abra o `App.js` arquivo no `src` diretório. Você já deve ver um monte de código lá. Além da pasta Cadence, este é um aplicativo simples de reação, exatamente como o que você geraria com o Create React App. Acabei de adicionar um estilo para economizar algum tempo: D

### ⚡Usando a Biblioteca de clientes de fluxo FCL ( )

Lembra do que mencionei sobre FCL no início? Ele padroniza as conexões da carteira, portanto você só precisa escrever o código de conexão da carteira *uma vez*. Vamos configurá-lo!

Importe os dois pacotes a seguir no arquivo app.js. Copie e cole logo abaixo de onde você está importando o logotipo do twitter.

`import * as fcl from "@onflow/fcl";
import * as types from "@onflow/types";`

A primeira importação é a biblioteca principal que nos ajuda a interagir com a blockchain Flow. A segunda biblioteca nos ajuda a converter tipos de dados javascript em tipos de dados compatíveis com fluxo / cadência.

Logo depois, adicione o seguinte:

`fcl.config({
  "flow.network": "testnet",
  "app.detail.title": "BottomShot", // Change the title!
  "accessNode.api": "https://rest-testnet.onflow.org",
  "app.detail.icon": "https://placekitten.com/g/200/200",
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn",
});`

É tudo o que você precisa para configurar seu aplicativo para ser compatível e trabalhar com a rede de teste Flow.

Aqui está o que `fcl.config()` está definindo:

- `"flow.network": "testnet"`
    
    configura a rede atual do dapp para testnet
    
- `"accessNode.api": "https://rest-testnet.onflow.org"`
    
    configura o URL do nó, que usamos para interagir com o blockchain.
    
- `"discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn"`
    
    conta ao dapp sobre a carteira que precisa ser usada para interagir com o dapp.
    
- `"app.detail.title": "BottomShot"`
    
    dá um nome ao dapp. Você pode alterá-lo para qualquer valor que desejar.
    
- E finalmente, `"app.detail.icon": "https://placekitten.com/g/200/200"`
    
    exibe um ícone para o aplicativo quando você está se autenticando com uma carteira.
    

Você pode conferir [esta parte](https://docs.onflow.org/fcl/reference/api/#common-configuration-keys) dos documentos oficiais de fluxo para saber mais sobre os valores de configuração.

### 🔓Crie o botão de conexão da carteira

Hora da autenticação mais fácil que você fará em um aplicativo da web ( sim, ainda mais fácil que o Metamask ).

O FCL faz autenticação *muito* fácil, literalmente, apenas 3 linhas de código e um botão em que você pode clicar.

Comece fazendo uma variável de estado com o `[useState](https://reactjs.org/docs/hooks-state.html)` gancho para armazenar a corrente `user`. Isso vai para o topo do seu `App()` função

  `const [ user, setUser ] = useState();`

Em seguida, vamos fazer as funções de autenticação ( conexão de carteira ) e usar o `setUser` funciona sempre que uma alteração de usuário é detectada. Adicione estes abaixo do `useState` variável:

  `const logIn = () => {
      fcl.authenticate();
  };

  const logOut = () => {
      fcl.unauthenticate();
  };

  useEffect(() => {
    // This listens to changes in the user objects
    // and updates the connected user
    fcl.currentUser().subscribe(setUser);
  }, [])`

**Lilico!** Se você está construindo para Lilico, verifique a rede do usuário. Com o Blocto, definimos a rede na configuração fcl. Em Lilico, os usuários podem alterar a rede eles mesmos. Veja como você pode rastrear para qual rede os usuários do Lilico mudam:

`const [network, setNetwork] = useState("");

useEffect(()=>{
    // This is an event listener for all messages that are sent to the window
    window.addEventListener("message", d => {
    // This only works for Lilico testnet to mainnet changes
      if(d.data.type==='LILICO:NETWORK') setNetwork(d.data.network)
    })
  }, [])`

Você pode usar o valor da rede para avisar o usuário se ele estiver na rede errada: D

**Nota** Quando você muda `Lilico` redes o endereço da sua conta muda. Portanto, quando a rede é alterada, a autenticação precisa ser feita novamente para obter o endereço da conta atualizado.

Logo depois, faça componentes de renderização para os dois botões:

  `const RenderLogin = () => {
    return (
      <div>
        <button className="cta-button button-glow" onClick={() => logIn()}>
          Log In
        </button>
      </div>
    );
  };

  const RenderLogout = () => {
    if (user && user.addr) {
      return (
        <div className="logout-container">
          <button className="cta-button logout-btn" onClick={() => logOut()}>
            ❎ {"  "}
            {user.addr.substring(0, 6)}...{user.addr.substring(user.addr.length - 4)}
          </button>
        </div>
      );
    }
    return undefined;
  };`

Coloque sua função de renderização de logout na parte superior e a função de renderização de login logo antes da `footer-container` div. Fiz meu botão de login desaparecer assim:

`{user && user.addr ? "Wallet connected!" : <RenderLogin />}`

É assim que o arquivo App.js deve ser agora:

```
//importing required libraries
import React, { useState, useEffect } from "react";
import './App.css';
import twitterLogo from "./assets/twitter-logo.svg";
import * as fcl from "@onflow/fcl";
import * as types from "@onflow/types";

const TWITTER_HANDLE = "_buildspace";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

fcl.config({
  "flow.network": "testnet",
  "app.detail.title": "BottomShot", // Change the title!
  "accessNode.api": "https://rest-testnet.onflow.org",
  "app.detail.icon": "https://placekitten.com/g/200/200",
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn",
});

function App() {

  const [ user, setUser ] = useState();

  const logIn = () => {
    fcl.authenticate();
  };

  const logOut = () => {
    fcl.unauthenticate();
  };

  useEffect(() => {
    // This listens to changes in the user objects
    // and updates the connected user
    fcl.currentUser().subscribe(setUser);
  }, [])

  const RenderLogin = () => {
    return (
      <div>
        <button className="cta-button button-glow" onClick={() => logIn()}>
          Log In
        </button>
      </div>
    );
  };

  const RenderLogout = () => {
    if (user && user.addr) {
      return (
        <div className="logout-container">
          <button className="cta-button logout-btn" onClick={() => logOut()}>
            ❎ {"  "}
            {user.addr.substring(0, 6)}...{user.addr.substring(user.addr.length - 4)}
          </button>
        </div>
      );
    }
    return undefined;
  };

  return (
    <div className="App">
      <RenderLogout />
      <div className="container">
        <div className="header-container">
          <div className="logo-container">
            <img src="./logo.png" className="flow-logo" alt="flow logo"/>
            <p className="header">✨Awesome NFTs on Flow ✨</p>
          </div>

          <p className="sub-text">The easiest NFT mint experience ever!</p>
        </div>

        {/* If not logged in, render login button */}
        {user && user.addr ? "Wallet connected!" : <RenderLogin />}

        <div className="footer-container">
            <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
            <a className="footer-text" href={TWITTER_LINK} target="_blank" rel="noreferrer">{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
}

export default App;

```

**Importante:** Você *pode* veja este aviso ao fazer login pela primeira vez. Você pode ignorá-lo ou adicionar as informações necessárias posteriormente.

[https://camo.githubusercontent.com/b1ccb7f6016f6d2ada537d66137dfd8a4ebf31df1d6708d81aae5e78bb90bd91/68747470733a2f2f692e696d6775722e636f6d2f724756324d424c2e706e67](https://camo.githubusercontent.com/b1ccb7f6016f6d2ada537d66137dfd8a4ebf31df1d6708d81aae5e78bb90bd91/68747470733a2f2f692e696d6775722e636f6d2f724756324d424c2e706e67)

Siga para `http://localhost:3000/` no seu navegador e você deve ver o seu botão de login sofisticado! Certifique-se de testá-lo fazendo login com sua conta 😎