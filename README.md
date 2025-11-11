
# Programação Estruturada
# Sistema de Controle de Produtos em C

Repositório da disciplina de **Programação Estruturada**.  
## 📚 Descrição
Projeto desenvolvido para a disciplina **Programação Estruturada**.  
O sistema faz controle de produtos lendo os dados de um arquivo texto (`produtos.txt`), aplicando:
- Vetores  
- Structs  
- Strings  
- Funções  
- Leitura de arquivos texto


#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_PRODUTOS 100
#define MAX_NOME 100

typedef struct {
    int codigo;
    char nome[MAX_NOME];
    float preco;
    int quantidade;
} Produto;

void imprimir_produtos(Produto produtos[], int n);
int buscar_por_codigo(Produto produtos[], int n, int codigo);
void ordenar_por_preco(Produto produtos[], int n);
int adicionar_produto(Produto produtos[], int *n, int max_produtos);
void limpar_buffer_stdin(void);
void carregar_produtos_iniciais(Produto produtos[], int *n);

int main() {
    Produto produtos[MAX_PRODUTOS];
    int n = 0;

    carregar_produtos_iniciais(produtos, &n);
    printf("Foram carregados %d produtos iniciais.\n\n", n);

    int opcao;
    do {
        printf("===== MENU DE CONTROLE DE PRODUTOS =====\n");
        printf("1. Adicionar produto\n");
        printf("2. Buscar produto por código\n");
        printf("3. Imprimir produtos\n");
        printf("4. Ordenar por preço e imprimir\n");
        printf("5. Sair\n");
        printf("Escolha uma opção: ");
        if (scanf("%d", &opcao) != 1) {
            printf("Entrada inválida. Tente novamente.\n");
            limpar_buffer_stdin();
            continue;
        }
        limpar_buffer_stdin();

        switch (opcao) {
            case 1:
                if (adicionar_produto(produtos, &n, MAX_PRODUTOS))
                    printf("Produto adicionado com sucesso.\n\n");
                else
                    printf("Falha ao adicionar produto.\n\n");
                break;

            case 2: {
                int codigo;
                printf("Digite o código do produto: ");
                if (scanf("%d", &codigo) != 1) {
                    printf("Entrada inválida.\n");
                    limpar_buffer_stdin();
                    break;
                }
                limpar_buffer_stdin();
                int idx = buscar_por_codigo(produtos, n, codigo);
                if (idx >= 0) {
                    Produto *p = &produtos[idx];
                    printf("\nProduto encontrado:\n");
                    printf("Código: %d\nNome: %s\nPreço: %.2f\nQuantidade: %d\n\n",
                           p->codigo, p->nome, p->preco, p->quantidade);
                } else {
                    printf("Produto com código %d não encontrado.\n\n", codigo);
                }
                break;
            }

            case 3:
                imprimir_produtos(produtos, n);
                break;

            case 4:
                ordenar_por_preco(produtos, n);
                printf("\nProdutos ordenados por preço (crescente):\n");
                imprimir_produtos(produtos, n);
                break;

            case 5:
                printf("Saindo...\n");
                break;

            default:
                printf("Opção inválida. Tente novamente.\n\n");
        }
    } while (opcao != 5);

    return 0;
}

void imprimir_produtos(Produto produtos[], int n) {
    if (n == 0) {
        printf("Nenhum produto cadastrado.\n\n");
        return;
    }
    printf("\n%-6s | %-36s | %-8s | %-10s\n", "Código", "Nome", "Preço", "Quantidade");
    printf("--------------------------------------------------------------------------\n");
    for (int i = 0; i < n; i++) {
        printf("%6d | %-36s | %8.2f | %10d\n",
               produtos[i].codigo, produtos[i].nome, produtos[i].preco, produtos[i].quantidade);
    }
    printf("\n");
}

int buscar_por_codigo(Produto produtos[], int n, int codigo) {
    for (int i = 0; i < n; i++)
        if (produtos[i].codigo == codigo)
            return i;
    return -1;
}

void ordenar_por_preco(Produto produtos[], int n) {
    if (n <= 1) return;
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - 1 - i; j++) {
            if (produtos[j].preco > produtos[j + 1].preco) {
                Produto tmp = produtos[j];
                produtos[j] = produtos[j + 1];
                produtos[j + 1] = tmp;
            }
        }
    }
}

int adicionar_produto(Produto produtos[], int *n, int max_produtos) {
    if (*n >= max_produtos) return 0;

    Produto p;
    printf("Digite o código: ");
    if (scanf("%d", &p.codigo) != 1) { limpar_buffer_stdin(); return 0; }
    limpar_buffer_stdin();

    if (buscar_por_codigo(produtos, *n, p.codigo) >= 0) {
        printf("Código já existente.\n");
        return 0;
    }

    printf("Digite o nome: ");
    fgets(p.nome, sizeof(p.nome), stdin);
    char *pn = strchr(p.nome, '\n');
    if (pn) *pn = '\0';

    printf("Digite o preço: ");
    if (scanf("%f", &p.preco) != 1) { limpar_buffer_stdin(); return 0; }
    limpar_buffer_stdin();

    printf("Digite a quantidade: ");
    if (scanf("%d", &p.quantidade) != 1) { limpar_buffer_stdin(); return 0; }
    limpar_buffer_stdin();

    produtos[*n] = p;
    (*n)++;
    return 1;
}

void limpar_buffer_stdin(void) {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}

/* Produtos pré-carregados no código */
void carregar_produtos_iniciais(Produto produtos[], int *n) {
    Produto base[] = {
        {1,  "Arroz 5kg",                25.50, 100},
        {2,  "Feijao 1kg",                8.30, 200},
        {3,  "Açúcar 1kg",                4.20, 150},
        {4,  "Farinha 1kg",               3.90,  80},
        {5,  "Macarrao 500g",             2.75, 120},
        {6,  "Molho de Tomate 340g",      3.40,  60},
        {7,  "Sal 1kg",                   1.50, 300},
        {8,  "Leite 1L",                  4.80, 180},
        {9,  "Oleo de Soja 900ml",        9.50,  90},
        {10, "Cafe 250g",                 7.20,  70},
        {11, "Biscoito 200g",             2.90, 110},
        {12, "Achocolatado 400g",        12.00,  50},
        {13, "Detergente 500ml",          2.25, 140},
        {14, "Sabao em Po 2kg",          15.00,  40},
        {15, "Papel Higienico 4un",       6.50, 130},
        {16, "Azeite 500ml",             18.30,  30},
        {17, "Queijo 1kg",               28.90,  25},
        {18, "Manteiga 200g",             6.20,  60},
        {19, "Presunto 200g",             5.50,  45},
        {20, "Frango Congelado 1kg",     12.80,  55}
    };
    int qtd = sizeof(base) / sizeof(base[0]);
    for (int i = 0; i < qtd; i++) {
        produtos[i] = base[i];
    }
    *n = qtd;
}
