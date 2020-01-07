## Criando front com react

## Na pasta root do projeto:
> yarn create react-app <Nome do App front>

> yarn add eslint prettier eslint-config-prettier eslint-plugin-prettier babel-eslint -D

> yarn eslint --init

```txt
Install it following the order:
- To check syntax, find problems, and enforce code style
- JavaScript modules (import/export)
- React
- Only the Browser must be checked
- Use a popular style guide
- Airbnb (https://github.com/airbnb/javascript)
- JavaScript
- And Yes/Y for everything else.
```

####  remove the package-lock.json (`since we are using yarn`)

### To update yarn.lock just run:
> yarn

## Configure:

### `.prettierrc as:`

```js
{
  "singleQuote": true,
  "trailingComma": "es5"
}
```

### `.eslintrc.js as:`

```js
module.exports = {
  env: {
    browser: true,
    es6: true,
  },
 -> extends: [
    'airbnb', 'prettier', 'prettier/react'
  ],
  globals: {
    Atomics: 'readonly',
    SharedArrayBuffer: 'readonly',
  },
->  parser: 'babel-eslint',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 2018,
    sourceType: 'module',
  },
  plugins: [
    'react',
->    'prettier',
  ],
->  rules: {
    'prettier/prettier': 'error',
    'react/jsx-filename-extension': [
      'warn', {
        extensions: ['.jsx','.js'],
      },
    ],
    'import/prefer-default-export': 'off',
    'no-param-reassign': 'off',
    'no-console': ["error", {allow: [ "tron" ] }]
  },
};

```

### `.editorconfig as:`

```java
root = true

[*]
end_of_line = lf
indent_style = space
indent_size = 2
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
```

## Now execute:

> yarn add react-router-dom styled-components react-icons axios prop-types polished


# `Installing react-hooks plugin for eslint`

> yarn add eslint-plugin-react-hooks -D

#### Now in eslint config file and in plugins part just add:

```java
  plugins: [
    'react',
    'prettier',
->  'react-hooks',
```

#### in rules part just add:

```java
rules: {
    'prettier/prettier': 'error',
    'react/jsx-filename-extension': [
      'warn', {
        extensions: ['.jsx','.js'],
      },
    ],
    'import/prefer-default-export': 'off',
    'no-param-reassign': 'off',
    'no-console': ['error', {allow: [ 'tron' ] }],
->  'react-hooks/rules-of-hooks': 'error',
->  'react-hooks/exhaustive-deps': 'warn',
```

# React hooks:

Diminuir verbosidade para compartilhar informações entre componentes (Redux, Apollo para GraphQL)

## useState

`useState:` Pertence a uma função para criar estados sem que seja necessario escrever em formato de classe

 - Criar estados dentro da propria função
 - Pode usar a `Const`
 - Estados separados para cada useCase
 - useState retorna array:
 - - Primeira posição retorna o estado em si
 - - Segunda posição, uma função para atualizar as informações do estado
 - - - Antes: this.setState
 - - - Agora: dentro de uma funcao 'handleAdd' usa-se: função([...estado, 'algo a ser adicionado']), exemplo:
 ```JavaScript
 import React, { useState } from 'react';

function App() {
  const [tech, setTech] = useState(['reactJS', 'React Native']);

  function handleAdd() {
    setTech([...tech, 'Node.js']);
  }

  return (
    <>
      <ul>
        {tech.map(t => (
          <li key={t}>{t}</li>
        ))}
      </ul>
      <button type="button" onClick={handleAdd}>
        Adicionar
      </button>
    </>
  );
}

export default App;
 ```
`Outro exemplo, onde usa-se 2 estados:`

```js
import React, { useState } from 'react';

function App() {
  const [tech, setTech] = useState(['reactJS', 'React Native']);
  const [newTech, setNewTech] = useState('');

  function handleAdd() {
    setTech([...tech, newTech]);
    setNewTech('');
  }

  return (
    <>
      <ul>
        {tech.map(t => (
          <li key={t}>{t}</li>
        ))}
      </ul>
      <input
        type="text"
        value={newTech}
        onChange={e => setNewTech(e.target.value)}
      />
      <button type="button" onClick={handleAdd}>
        Adicionar
      </button>
    </>
  );
}

export default App;

```
## useEffect

`useEffect:` Sobrepoe os metodos do ciclo de vida da aplicação (componentDidMount, componentDidUpdate, componentWillMount, componentWillUnMount, etc)

`useEffect(função, monitoraMudançaDeEstado) => useEffect(() => {função faz algo}, [estado a ser monitorado])`

 - Pode ser usado em formato de função, sendo:
 - - Primeiro parametro a funcao a ser executada
 - - Segundo parametro quando a ser executada a funcao

 ```js
 "componentDidUpdate"
   useEffect(() => {
    localStorage.setItem('tech', JSON.stringify(tech));
  }, [tech]); <- Estado sendo monitorado
 ```
 - Exemplo para quando você não precisar monitorar algum estado:

 ```js
 "componentDidMount"
   useEffect(() => {
    const getTech = localStorage.getItem('tech');

    if (getTech) {
      setTech(JSON.parse(getTech));
    }
  }, []); <- Vazio
 ```

 - exemplo para quando você precisa retornar algo assim que o componente deixa de ser montado, como faziamos no componentWillUnMount, no caso de um EventListener ou monta-lo com componentWillMount.

 ```js
 "componentWillUnMount"
   useEffect(() => {
    const getTech = localStorage.getItem('tech');

    if (getTech) {
      setTech(JSON.parse(getTech));
    }

    return () => {}; <- Retorno de função
  }, []);
 ```
  ```js
 "componentWillMount"
   useEffect(() => {
    localStorage.setItem('tech', JSON.stringify(tech));

  return () => {}; <- Retorno de função
  }, [tech]);
 ```

 #### Com a mudança do codigo acima, podemos então deixar o codigo final assim:

 `E caso tenhamos algo no localStorage ele vai carregar os dados na <li></li>`

 ```JavaScript
 import React, { useState, useEffect } from 'react';

function App() {
  const [tech, setTech] = useState([]);
  const [newTech, setNewTech] = useState('');

  function handleAdd() {
    setTech([...tech, newTech]);
    setNewTech('');
  }

  useEffect(() => {
    const getTech = localStorage.getItem('tech');

    if (getTech) {
      setTech(JSON.parse(getTech));
    }
  }, []);

  useEffect(() => {
    localStorage.setItem('tech', JSON.stringify(tech));
  }, [tech]);

  return (
    <>
      <ul>
        {tech.map(t => (
          <li key={t}>{t}</li>
        ))}
      </ul>
      <input
        type="text"
        value={newTech}
        onChange={e => setNewTech(e.target.value)}
      />
      <button type="button" onClick={handleAdd}>
        Adicionar
      </button>
    </>
  );
}

export default App;
 ```
## useMemo

`useMemo:` Para ser usado quando se precisa contabilizar algo e para que não seja contabilizado sempre que a pagina for renderizada, mas sim so quando a informação for realmente alterada

`const techSize = useMemo(() => valor, [Baseado na mudança desse estado]);`

:

`const techSize = useMemo(() => tech.length, [tech]);`


Usando ela no codigo:

`<strong>Você tem {techSize} tecnologias</strong>`

## useCallBack

`useCallBack:` Similar ao useMemo, mas ao inves de retornar um unico valor, ele retorna uma função.

 - Serve para funções, parecida com a `handleAdd` do nosso codigo
 - serve para substituir functions sendo chamadas dentro de outra function
 - a function padrão é montada toda vez que um valor do estado mudar, isso poderá usar muito recurso e não é viavel

 #### forma de usar:

 Alterar:
 ```js
 function handleAdd() {
    setTech([...tech, newTech]);
    setNewTech('');
  }
 ```

 Para:
  ```js
 const handleAdd = useCallBack( () => {
    setTech([...tech, newTech]);
    setNewTech('');
  }, [newTech, tech] );
 ```
