from flask import Flask, render_template, request, redirect, url_for, session

app = Flask(__name__)
app.secret_key = 'chave_secreta'

# Simulação de banco de dados de produtos
produtos = [
    {'id': 1, 'nome': 'Notebook Gamer', 'preco': 4500.00},
    {'id': 2, 'nome': 'Smartphone', 'preco': 2500.00},
    {'id': 3, 'nome': 'Fone Bluetooth', 'preco': 350.00},
]

@app.route('/')
def index():
    return render_template('index.html', produtos=produtos)

@app.route('/produto/<int:produto_id>')
def produto(produto_id):
    produto = next((p for p in produtos if p['id'] == produto_id), None)
    return render_template('produto.html', produto=produto)

@app.route('/adicionar/<int:produto_id>')
def adicionar(produto_id):
    if 'carrinho' not in session:
        session['carrinho'] = []
    session['carrinho'].append(produto_id)
    session.modified = True
    return redirect(url_for('index'))

@app.route('/carrinho')
def carrinho():
    carrinho_itens = [p for p in produtos if p['id'] in session.get('carrinho', [])]
    return render_template('carrinho.html', carrinho=carrinho_itens)

@app.route('/finalizar')
def finalizar():
    session.pop('carrinho', None)
    return 'Pedido Finalizado!'

if __name__ == '__main__':
    app.run(debug=True)
