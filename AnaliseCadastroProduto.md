## Análise do código do CadastroProduto.java
-  **Projeto analisado presente no repositório** : https://github.com/kayrocesar/TP1-TDD

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

&emsp;&emsp;Após análise vejo que o código que fizemos está bem estruturado, porém poderia ter algumas melhorias, como:

- **Modularidade**:
	- O código da classe CadastroProduto tem uma boa modularidade, principalmente devido à separação de responsabilidades dos métodos específicos.
- **Duplicidade:**
	- Não possui;
- **Portabilidade**:
	- O código é portável, pois não depende de características específicas de uma plataforma ou sistema operacional. Ele deve funcionar em diferentes ambientes de execução que suportam Java.
- **Extensabilidade**:
	- A extensibilidade do código pode ser melhorada considerando os seguintes pontos:
		- Validação de Entrada:
		- Mensagens de Exceção:
- **Simplicidade e clareza**:
	- O código é relativamente simples e fácil de entender.
	- Os nomes das classes, métodos e variáveis são descritivos, o que facilita a compreensão do código.

- **Idiomatico**
  	-O código segue as convenções de nomenclatura do Java, o que é uma prática boa e ajuda na legibilidade.
	- O uso de exceções específicas (DescricaoEmBrancoException e ValorInvalidoException) é uma boa prática, pois fornece informações claras sobre o tipo de problema encontrado.
- Boas Interfaces:
	- A interface pública (CadastroProduto) parece ser simples e intuitiva.
	- A verificação de validação nos métodos (cadastrarProduto) ajuda a manter a integridade dos dados e evita que dados inválidos sejam armazenados.
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

