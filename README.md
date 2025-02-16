<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <title>Shopping List</title>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.bundle.min.js"></script>
    <style>
        body {
            background-color: #90EE90; /* Light Green */
        }
    </style>

</head>

<body>

    <div class="container mt-5">
        <h1 class="text-center">Exemplo: Lista de Compras</h1>
        <div id="app" class="mt-4">
            <form v-on:submit.prevent="addItem" class="border p-4 rounded shadow-sm">
                <div class="form-group">
                    <label for="itemNome">O que você precisa?</label>
                    <input type="text" id="itemNome" class="form-control" required placeholder="Nome do item..."
                        v-model="itemNome">
                </div>
                <div class="form-group">
                    <label for="itemQuant">Quantidade?</label>
                    <input type="number" id="itemQuant" class="form-control" placeholder="Número/qtde de itens..."
                        v-model="itemQuant">
                </div>
                <button type="submit" class="btn btn-primary mr-2">Adicionar item</button>
                <button type="button" class="btn btn-secondary" v-on:click="finaliza()">Finalizar</button>
                <button type="button" class="btn btn-info" v-on:click="verCarrinho()">Ver Carrinho</button>

            </form>

            <div class="mt-4">
                <h4>Lista de compras:</h4>
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th>Nome do Item</th>
                            <th>Quantidade</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in shoppingList">
                            <td>{{ item.nome }}</td>
                            <td>{{ item.quant }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        // informe aqui a sua URL do projeto do banco de dados Supabase
        const apiUrlCarrinho = 'https://ytxvizeegmpndakfwypl.supabase.co/rest/v1/carrinho';
        const apiKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Inl0eHZpemVlZ21wbmRha2Z3eXBsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3MzgyNzU2MjAsImV4cCI6MjA1Mzg1MTYyMH0.JExUpOj5KaKcreUVx-VPX1CrHD6Pj0g1hsA3S2KJjNE'; // informe aqui a sua API-KEY
        const apiAuthorization = 'Bearer ' + apiKey;
        const app = Vue.createApp({
            data() {
                return {
                    itemNome: null,
                    itemQuant: null,
                    shoppingList: [] // inicializa o carrinho
                };
            },
            methods: {
                addItem() {
                    let item = {
                        nome: this.itemNome,
                        quant: this.itemQuant
                    };
                    this.shoppingList.push(item); // adiciona o item na lista
                    this.itemNome = null; // limpa os dados do item
                    this.itemQuant = null;
                },
                finaliza() {
                    console.log("CARRINHO ANTES=", JSON.stringify(this.shoppingList));
                    var carrinho = this; // esta variavel retem os dados da app Vue.js (o modelo) para acesso no escopo do Ajax
                    $.ajax({ // aqui vc acessa o supabase e chama a API para incluir os dados no banco
                        type: 'post',
                        url: apiUrlCarrinho,
                        data: JSON.stringify(carrinho.shoppingList), // O método JSON.stringify() converte valores em javascript para uma String JSON.
                        contentType: 'application/json; charset=utf-8',
                        crossDomain: true,
                        headers: {
                            'apikey': apiKey,
                            'Authorization': apiAuthorization,
                            'Content-Type': 'application/json',
                            'Access-Control-Allow-Origin': '*', // codigo para evitar CORS
                            'Access-Control-Allow-Headers': 'authorization, x-client-info, apikey, content-type'
                        },
                        success: function (dados, xhr, textStatus) {
                            window.alert("Ok. GRAVADOS " + carrinho.shoppingList.length + " itens.");
                            carrinho.shoppingList = []; // limpa o carrinho 
                            console.log("CARRINHO DEPOIS", JSON.stringify(carrinho.shoppingList));
                        },
                        error: (erro) => {
                            window.alert("Ocorreu um erro: " + erro.message);
                        }
                    });
                },
                verCarrinho() {
                    console.log("Itens no carrinho:", JSON.stringify(this.shoppingList));
                    if (this.shoppingList.length === 0) {
                        alert("O carrinho está vazio.");
                    } else {
                        let message = "Itens no carrinho:\n";
                        this.shoppingList.forEach(item => {
                            message += `${item.nome}: ${item.quant}\n`;
                        });
                        alert(message);
                    }
                }
            }
        });
        app.mount("#app");
    </script>
</body>

</html>pras.
