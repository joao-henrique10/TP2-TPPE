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
  - A validação de entrada pode ser mais granular. Em vez de verificar cada campo individualmente, você pode criar métodos na classe Produto para validar diferentes aspectos, como validar se uma string está em branco ou se um valor numérico é válido. Isso pode tornar o código mais modular e fácil de manter.
- Tratamento de Exceções:
  - O tratamento de exceções pode ser melhorado. No momento, você está lançando exceções específicas, o que é bom, mas seria útil adicionar mensagens informativas às exceções para que os desenvolvedores saibam exatamente qual campo está causando o problema.
- Utilização de Métodos Utilitários:
  - Para melhorar a legibilidade do código, você pode criar métodos utilitários para verificar se uma string está em branco, se um valor numérico é inválido, etc. Isso pode reduzir a redundância e melhorar a manutenção.
- Usar Enums para Categorias:
  - Se possível, considere o uso de Enums para representar as categorias dos produtos em vez de permitir que elas sejam nulas. Isso pode tornar o código mais seguro e evitar possíveis NullPointerExceptions.

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

