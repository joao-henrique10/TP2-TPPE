## Análise do código RastreamentoLoteValidade.java

-  **Projeto analisado presente no repositório** : https://github.com/kayrocesar/TP1-TDD
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

- **Modularidade**:
	- A classe RastreamentoLoteValidade tem uma boa modularidade, pois cada método tem uma responsabilidade clara e específica. 
- **Duplicidade**:
	- O cóigo da classe não possui duplicidade;
- **Portabilidade**:
	- O código é portável, pois não depende de características específicas de uma plataforma ou sistema operacional. Ele deve funcionar em diferentes ambientes de execução que suportam Java, não alterando as características da classe java ao depender do dispositivo e plataforma.
- **Extensabilidade**:
	- A extensibilidade do código pode ser melhorada considerando os seguintes pontos:
		- Mensagens de Log;
		- Atualização de Preço;
    		- Modificação do sistema de alerta para possibilitar adição de novos alertas específicos a partir dos parâmetros passados;  
- **Simplicidade e clareza**:
  	- O código é relativamente simples e fácil de entender.
	- O uso de métodos bem-nomeados contribui para a legibilidade.
   	- Seria possível melhorar a nomeclatura do método emiteAlertLoteVencimento para verificarLoteProximoVencimento para deixar claro que o alerta é emitido quando próximo de vencimento; 
 -  **Idiomático:**
   	- O código segue as convenções de nomenclatura do Java.
	- A utilização de tipos de dados apropriados, como ArrayList para coleções dinâmicas, é boa prática.
- **Boas Interfaces:**
  	- A interface pública fornece métodos que parecem ser intuitivos, como quantidadeTotalProduto e emiteAlertLoteVencimento.
	-A atualização do preço de venda dos produtos próximos ao vencimento é um comportamento interessante e está encapsulado no método emiteAlertLoteVencimento.
- Nomenclatura de Métodos:
    - A nomenclatura dos métodos está bastante clara, mas consideramos adicionar um prefixo descritivo, como "calcular" ou "verificar", para tornar o propósito do método ainda mais óbvio.
    - Seria possível melhorar a nomeclatura do método emiteAlertLoteVencimento para verificarLoteProximoVencimento para utilizar o termo no infinitivo, característico de verbo e também para padronizar no português a palavra alerta. 
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

    public boolean verificarLoteProximoVencimento(ArrayList<Produto> produtos, Integer qdtLotesDiferentes) {
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
