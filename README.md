import ast
import matplotlib.pyplot as plt
import networkx as nx

# Función para construir el árbol desde la expresión
def build_expression_tree(expr):
    tree = ast.parse(expr, mode='eval')
    return tree.body

# Función para obtener el símbolo del operador
def get_operator_symbol(op):
    symbols = {
        ast.Add: '+',
        ast.Sub: '-',
        ast.Mult: '*',
        ast.Div: '/',
        ast.Pow: '^',
        ast.Mod: '%'
    }
    return symbols.get(type(op), '?')

# Agrega nodos y conexiones al grafo
def add_nodes_edges(node, graph, parent=None):
    if isinstance(node, ast.BinOp):
        label = get_operator_symbol(node.op)
        graph.add_node(id(node), label=label)
        if parent:
            graph.add_edge(id(parent), id(node))
        add_nodes_edges(node.left, graph, node)
        add_nodes_edges(node.right, graph, node)

    elif isinstance(node, ast.Constant):  # Para Python 3.8+
        graph.add_node(id(node), label=str(node.value))
        if parent:
            graph.add_edge(id(parent), id(node))

# Dibuja el árbol
def draw_expression_tree(expr):
    root = build_expression_tree(expr)
    graph = nx.DiGraph()
    add_nodes_edges(root, graph)

    labels = nx.get_node_attributes(graph, 'label')
    pos = nx.nx_agraph.graphviz_layout(graph, prog='dot')  # Usa Graphviz
    nx.draw(graph, pos, labels=labels, with_labels=True,
            node_size=2000, node_color='lightgreen', font_size=12, font_weight='bold')
    plt.title(f"Árbol de expresión: {expr}")
    plt.show()

# ⚙️ Expresión de entrada
expression = "(4 + 2) * (6 / (1 + 2))"
draw_expression_tree(expression)
# arbol_expresion
