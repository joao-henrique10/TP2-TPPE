# TP2-TPPE

## Integrantes:

| Aluno                 | Matrícula | 
|-----------------------|-----------|
|Adne Moretti Moreira|200013181|
|Gabriel Moretti de Souza|200018205|
|João Henrique Marques Calzavara|200067923|

## Análise do código do CadastroProduto.java
-  **Projeto analisado presente no repositório** : https://github.com/kayrocesar

```
package app;

import Exceptions.DescricaoEmBrancoException;
import Exceptions.ValorInvalidoException;
import model.Produto;


import java.util.ArrayList;

public class CadastroProduto {

    private final ArrayList<Produto> produtos;


    public CadastroProduto() {
        this.produtos = new ArrayList<>();
    }

    public void cadastrarProduto(Produto produto) throws DescricaoEmBrancoException, ValorInvalidoException {

        if (produto.getNome().isBlank() || produto.getDescricao().isBlank() || produto.getCodigoBarras().isBlank() || produto.getCusto().isNaN() ||
                produto.getPrecoVenda().isNaN() || produto.getCategoria() == null || produto.getFornecedor() == null) {
            throw new DescricaoEmBrancoException("Nenhum campo pode estar vazio!");
        }
        if (produto.getCusto() <= 0.0 || produto.getPrecoVenda() <= 0.0 || produto.getQtd() <= 0 || produto.getQtdMinima() <= 0) {
            throw new ValorInvalidoException("Valor não pode ser inferior ou igual a zero!");
        }

        this.produtos.add(produto);

    }

    public ArrayList<Produto> getProdutos() {
        return this.produtos;
    }

}

```

&emsp;&emsp;Após análise vejo que o código que fizemos está bes estruturado, porém poderia ter algumas melhorias, como:
- Validação de Entrada:
  - A validação de entrada pode ser mais granular. Em vez de verificar cada campo individualmente, podemos criar métodos na classe Produto para validar diferentes aspectos, como validar se uma string está em branco ou se um valor numérico é válido. Isso pode tornar o código mais modular e fácil de manter.
- Tratamento de Exceções:
  - O tratamento de exceções pode ser melhorado. No momento, estamos lançando exceções específicas, o que é bom, mas seria útil adicionar mensagens informativas às exceções para que os desenvolvedores saibam exatamente qual campo está causando o problema.
- Utilização de Métodos Utilitários:
  - Para melhorar a legibilidade do código, podemos criar métodos utilitários para verificar se uma string está em branco, se um valor numérico é inválido, etc. Isso pode reduzir a redundância e melhorar a manutenção.
- Usar Enums para Categorias:
  - Se possível, seria bom considerarmos o uso de Enums para representar as categorias dos produtos em vez de permitir que elas sejam nulas. Isso pode tornar o código mais seguro e evitar possíveis NullPointerExceptions.

&emsp;&emsp;Abaixo uma versão modificada do nosso código com algumas dessas possíveis melhorias:
```
package app;

import Exceptions.DescricaoEmBrancoException;
import Exceptions.ValorInvalidoException;
import model.Produto;

import java.util.ArrayList;

public class CadastroProduto {

    private final ArrayList<Produto> produtos;

    public CadastroProduto() {
        this.produtos = new ArrayList<>();
    }

    public void cadastrarProduto(Produto produto) throws DescricaoEmBrancoException, ValorInvalidoException {
        validarProduto(produto);

        this.produtos.add(produto);
    }

    private void validarProduto(Produto produto) throws DescricaoEmBrancoException, ValorInvalidoException {
        if (produto.isCamposEmBranco()) {
            throw new DescricaoEmBrancoException("Nenhum campo pode estar vazio!");
        }

        if (produto.isValoresInvalidos()) {
            throw new ValorInvalidoException("Valor não pode ser inferior ou igual a zero!");
        }
    }

    public ArrayList<Produto> getProdutos() {
        return this.produtos;
    }
}

```

## Análise do código GestaoTransacoes.java

-  **Projeto analisado presente no repositório** : https://github.com/kayrocesar

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

&emsp;&emsp;Após análise vejo que o código que fizemos está bes estruturado, porém poderia ter algumas melhorias, como:

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

## Análise do código RastreamentoLoteValidade.java

-  **Projeto analisado presente no repositório** : https://github.com/kayrocesar
```
package app;

import model.Produto;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashSet;
import java.util.Set;

public class RastreamentoLoteValidade {

    public  Integer quantidadeTotalProduto(Produto produto){
        return produto.getQtd();
    }
    public Integer quantidadeTotalProdutoLote(ArrayList<Produto> produtos, Integer numeroLote){
        Integer total=0;
        for (Produto produto: produtos){
            if (produto.getLote().equals(numeroLote)){
                total+=produto.getQtd();
            }
        }
        return total;
    }
    public boolean emiteAlertLoteVencimento(ArrayList<Produto> produtos, Integer qdtLotesDiferentes) {
        Boolean controle=false;
        ArrayList<Boolean> lotesProximosVencimento = new ArrayList<>(Collections.nCopies(qdtLotesDiferentes, false));
        LocalDate dataAtual = LocalDate.now();

        for (Produto produto: produtos){
            Boolean proximoAoVencimento =verificarDistanciaDias(dataAtual,produto.getDataVencimento(),10);
            if (proximoAoVencimento){
                lotesProximosVencimento.set(produto.getLote() - 1, true);
                produto.setPrecoVenda(produto.getPrecoVenda()*0.8);
                System.out.println("Atenção o produto " + produto.getNome() + " com validade "
                + produto.getDataVencimento() + " está próximo ao vencimento, o preço de venda foi atualizado de R$"
                + produto.getPrecoVenda()*1.25 + " para R$" +produto.getPrecoVenda()+ "\n");
            }
        }
        for (int i=0; i <lotesProximosVencimento.size(); i++){
            if (lotesProximosVencimento.get(i).equals(true)){
                System.out.println("Os preços dos itens do lote "+ (i+1) +
                        " foram atualizados com um desconto de 20%, pois estavam próximos à data de vencimento." );
                controle=true;
            }
        }

        return controle;

    }
    private boolean verificarDistanciaDias(LocalDate data1, LocalDate data2, long minDiasDeDiferenca) {
        long diferenca = Math.abs(ChronoUnit.DAYS.between(data1, data2));
        return diferenca <= minDiasDeDiferenca;

    }
}
```

&emsp;&emsp;Após análise vejo que o código que fizemos está bem estruturado, porém poderia ter algumas melhorias, como:

- Nomenclatura de Métodos:
    - A nomenclatura dos métodos está bastante clara, mas consideramos adicionar um prefixo descritivo, como "calcular" ou "verificar", para tornar o propósito do método ainda mais óbvio.
- Constantes para Números Mágicos:
    - Os números mágicos no código (como 10 e 0.8) podem ser substituídos por constantes com nomes descritivos para melhorar a legibilidade e facilitar futuras modificações.
- Uso de Enums:
    - Se possível, seria bom considerar o uso de Enums para representar o estado do lote (por exemplo, "próximo ao vencimento"). Isso pode tornar o código mais legível e facilitar futuras alterações.
- Mensagens de Log:
    - As mensagens de log no método emiteAlertLoteVencimento são informativas, mas considerando que o método retorna um booleano indicando se algum lote está próximo do vencimento, talvez seria melhor tornar essas mensagens mais consistentes e informativas.

&emsp;&emsp;Abaixo uma versão modificada do nosso código com algumas dessas possíveis melhorias:

```
package app;

import model.Produto;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;
import java.util.ArrayList;
import java.util.Collections;

public class RastreamentoLoteValidade {

    private static final double DESCONTO_PROXIMO_VENCIMENTO = 0.2;
    private static final int DIAS_PROXIMO_VENCIMENTO = 10;

    public Integer calcularQuantidadeTotalProduto(Produto produto) {
        return produto.getQtd();
    }

    public Integer calcularQuantidadeTotalProdutoLote(ArrayList<Produto> produtos, Integer numeroLote) {
        Integer total = 0;
        for (Produto produto : produtos) {
            if (produto.getLote().equals(numeroLote)) {
                total += produto.getQtd();
            }
        }
        return total;
    }

    public boolean verificarAlertaLoteVencimento(ArrayList<Produto> produtos, Integer qdtLotesDiferentes) {
        boolean controle = false;
        ArrayList<Boolean> lotesProximosVencimento = new ArrayList<>(Collections.nCopies(qdtLotesDiferentes, false));
        LocalDate dataAtual = LocalDate.now();

        for (Produto produto : produtos) {
            boolean proximoAoVencimento = verificarDistanciaDias(dataAtual, produto.getDataVencimento(), DIAS_PROXIMO_VENCIMENTO);
            if (proximoAoVencimento) {
                lotesProximosVencimento.set(produto.getLote() - 1, true);
                produto.setPrecoVenda(produto.getPrecoVenda() * (1 - DESCONTO_PROXIMO_VENCIMENTO));
                System.out.println("Atenção: O produto " + produto.getNome() + " com validade "
                        + produto.getDataVencimento() + " está próximo ao vencimento. O preço de venda foi atualizado de R$"
                        + produto.getPrecoVenda() / (1 - DESCONTO_PROXIMO_VENCIMENTO) + " para R$"
                        + produto.getPrecoVenda() + "\n");
            }
        }

        for (int i = 0; i < lotesProximosVencimento.size(); i++) {
            if (lotesProximosVencimento.get(i)) {
                System.out.println("Os preços dos itens do lote " + (i + 1) +
                        " foram atualizados com um desconto de " +
                        (DESCONTO_PROXIMO_VENCIMENTO * 100) + "%, pois estavam próximos à data de vencimento.");
                controle = true;
            }
        }

        return controle;
    }

    private boolean verificarDistanciaDias(LocalDate data1, LocalDate data2, long minDiasDeDiferenca) {
        long diferenca = Math.abs(ChronoUnit.DAYS.between(data1, data2));
        return diferenca <= minDiasDeDiferenca;
    }
}
```
