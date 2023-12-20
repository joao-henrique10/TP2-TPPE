## Análise do código GestaoTransacoes.java

-  **Projeto analisado presente no repositório** : https://github.com/kayrocesar/TP1-TDD

```
package app;

import java.time.LocalDate;
import java.util.ArrayList;

import Exceptions.ValorInvalidoException;
import enums.TipoTransacao;
import model.ProdutoQuantidade;
import model.Transacao;

public class GestaoTransacoes {
	
	private ArrayList<Transacao> transacoes; 
	
	public GestaoTransacoes() {
		this.transacoes = new ArrayList<>(); 
	}
	
	public ArrayList<Transacao> getTransacoes(){
		return transacoes; 	
	}
	
	public Transacao getUltimaTransacao() {
		return this.transacoes.get(this.transacoes.size()-1); 
	}
	
    public int getQuantidadeTransacao() {
		return this.transacoes.size(); 
    }

    public void cadastrarTransacao(LocalDate data, ArrayList<ProdutoQuantidade> produtos, TipoTransacao tipoTransacao) {
    	Transacao transacao = new Transacao(data, produtos, tipoTransacao); 
    	atualizarEstoque(transacao); 
    	this.transacoes.add(transacao); 
    }
    
    public void cadastrarTransacao(Transacao transacao) {
    	atualizarEstoque(transacao);
    	this.transacoes.add(transacao);
    }
   
    public void atualizarEstoque(Transacao transacao) {
    	if(transacao.getTipoTransacao() == TipoTransacao.VENDA) {
	    	for(ProdutoQuantidade elemento: transacao.getProdutos()) {
	    		elemento.getProduto().diminuirQtd(elemento.getQuantidade());
	    	}
    	}
    	else {
	    	for(ProdutoQuantidade elemento: transacao.getProdutos()) {
	    		elemento.getProduto().adicionarQtd(elemento.getQuantidade());
    	}
    
    	}
    }
    
    public void validarTransacao(Transacao transacao) throws ValorInvalidoException{
    	for (ProdutoQuantidade produtoQtd : transacao.getProdutos()) {
			if(produtoQtd.getQuantidade() < 0) {
				throw new ValorInvalidoException("O valor da transacao não pode ser negativo para não ajustes.");
			}    			
		}
    }
    
}
```

&emsp;&emsp;Após análise vejo que o código que fizemos está bem estruturado, porém poderia ter algumas melhorias, como:

- **Modularidade**:
	- O código da classe GestaoTransacoes tem uma boa modularidade, principalmente devido à separação de responsabilidades dos métodos específicos.
- **Duplicidade**:
	- Não possui;
- **Portabilidade**:
	- O código é portável, pois não depende de características específicas de uma plataforma ou sistema operacional. Ele deve funcionar em diferentes ambientes de execução que suportam Java.
- **Extensabilidade**:
	- A extensibilidade do código pode ser melhorada considerando os seguintes pontos:
		- Validação de Transação;
		- Atualização de Estoque:
- **Simplicidade e clareza**:
  	- O código é relativamente claro e fácil de entender.
	- A utilização de métodos bem-nomeados contribui para a legibilidade.
 -  **Idiomático:**
   	- O código segue as convenções de nomenclatura do Java.
	- O uso de exceções específicas (ValorInvalidoException) é uma boa prática para indicar problemas específicos.
- **Boas Interfaces:**
  	- A interface pública parece ser razoavelmente intuitiva, fornecendo métodos para acessar informações sobre transações e cadastrar novas transações.
	- A ideia de separar a lógica de atualização de estoque em um método separado (atualizarEstoque) é uma boa prática, pois mantém a modularidade e a legibilidade.
- Validação de Entrada:
    - Assim como no código anterior, a validação de entrada pode ser mais granular. Podemos criar métodos utilitários na classe Transacao para verificar se a data é nula e se a lista de produtos está vazia, por exemplo.
- Uso de Enums:
    - O uso de enums para representar o tipo de transação é uma boa prática. No entanto, temos que certificar de que a classe Transacao esteja usando o enum TipoTransacao em vez de um simples String.
- Método atualizarEstoque:
    - O método atualizarEstoque faz um bom trabalho ao iterar sobre os produtos da transação e atualizar o estoque. No entanto, consideramos extrair a lógica de atualização do estoque para a classe Produto para manter a coesão e seguir o princípio de responsabilidade única.
- Validação de Transação:
    - O método validarTransacao verifica se a quantidade de produtos em uma transação é negativa. Consideramos lançar uma exceção mais específica (talvez QuantidadeNegativaException) para fornecer informações mais detalhadas sobre o erro.

&emsp;&emsp;Abaixo uma versão modificada do nosso código com algumas dessas possíveis melhorias:

```
package app;

import java.time.LocalDate;
import java.util.ArrayList;

import Exceptions.ValorInvalidoException;
import enums.TipoTransacao;
import model.ProdutoQuantidade;
import model.Transacao;

public class GestaoTransacoes {

    private ArrayList<Transacao> transacoes;

    public GestaoTransacoes() {
        this.transacoes = new ArrayList<>();
    }

    public ArrayList<Transacao> getTransacoes() {
        return transacoes;
    }

    public Transacao getUltimaTransacao() {
        return this.transacoes.get(this.transacoes.size() - 1);
    }

    public int getQuantidadeTransacao() {
        return this.transacoes.size();
    }

    public void cadastrarTransacao(LocalDate data, ArrayList<ProdutoQuantidade> produtos, TipoTransacao tipoTransacao)
            throws ValorInvalidoException {
        Transacao transacao = new Transacao(data, produtos, tipoTransacao);
        validarTransacao(transacao);
        atualizarEstoque(transacao);
        this.transacoes.add(transacao);
    }

    public void cadastrarTransacao(Transacao transacao) throws ValorInvalidoException {
        validarTransacao(transacao);
        atualizarEstoque(transacao);
        this.transacoes.add(transacao);
    }

    private void atualizarEstoque(Transacao transacao) {
        for (ProdutoQuantidade elemento : transacao.getProdutos()) {
            elemento.getProduto().atualizarEstoque(elemento.getQuantidade(), transacao.getTipoTransacao());
        }
    }

    private void validarTransacao(Transacao transacao) throws ValorInvalidoException {
        for (ProdutoQuantidade produtoQtd : transacao.getProdutos()) {
            if (produtoQtd.getQuantidade() < 0) {
                throw new ValorInvalidoException("A quantidade da transacao não pode ser negativa.");
            }
        }
    }
}
```
