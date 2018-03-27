---
layout: post
title:  "React-Native Definindo uma State"
date:   2018-03-27 04:00:00 -0300
tags: react-native, state, mobile, android
categories: [react-native]
---

Nesse tutorial vou mostrar como criar um state simples usando react-native

## O que vamos construir?

Nos vamos construir uma aplicação simples onde mostramos palavras aleatorias na tela do celular

Veja uma rapida desmonstração do que sera criado.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/NjtgpBkgZak" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

- ### Basic Setup

Crie o projeto
```
react-native init myfood
```

- ### App.js

Em **app.js** adicione as linhas de codigos abaixo.

```js
//importe component react
import React, { Component } from 'react';
// imports do react-native
import {  
  Text,
  View,
  StyleSheet
} from 'react-native';


//class responsavel por renderizar as informação na tela do celular
export default class App extends Component {
  render() {
    return (
      <View>     
        {/* Chama o Component Janta*/}   
        <Janta />      
      </View>
    );
  }
}

// Criando Componente Janta
class Janta extends Component {
  //Definindo o State
  constructor(props) {
    // Usando super para ser usado a props em todos os lugares do component
    super(props);
    // criado o state inicial vazio
    this.state = { comida:'' };
    // definindo o array com as opções de comida
    let comidas = ['Pizza', 'Lasanha', 'Burger', 'Sopa', 'Arroz'];

    //Mudando o state com as informaçoes do array comidas
    setInterval(() => {
      // Substituindo o state anterior
      this.setState(previousState => {
        //Pegando as comidas em aleatorio
        let n = Math.floor(Math.random() * comidas.length);
        // Retornando o novo state com as informaçoes de comidas que foi pego aleatoriamente
        return {comida: comidas[n]};

      });
      //Quantidade tempo que vai mudar o state em 1 e 1 segundo
    }, 1000);
  }

  render() {
    return (      
      <View style={styles.padrao}>
        <Text style={styles.azulGrande}>
          Hoje voce vai jantar     
        </Text>  
        <Text style={styles.vermelho}>
        {/* Chama o state Comida*/} 
          {this.state.comida}      
        </Text>       
      </View>
    );
  }
}

//create constante
//create folha de estilo
const styles = StyleSheet.create({
  azulGrande: {
    color: '#0000FF',
    fontSize:30,
    textAlign:'center'
    
  },
  vermelho: {
    color:'#FF0000',
    textAlign:'center',
    fontSize:20
  },
  padrao: {
    padding:80   
  }
});
```

- ### Final

Rode o projeto
```
react-native run-android
```

Todas as linhas estao comentadas para facilitar a compreensão realize o teste faça melhorias. e Até a proxima.