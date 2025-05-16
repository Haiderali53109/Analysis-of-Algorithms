class Node:
    def __init__(self, name, phone_number, color='red', parent=None):
        self.name = name
        self.phone_number = phone_number
        self.color = color  # 'red' or 'black'
        self.parent = parent
        self.left = None
        self.right = None

class RedBlackTree:
    def __init__(self):
        self.NIL = Node(name=None, phone_number=None, color='black')
        self.root = self.NIL

    def insert(self, name, phone_number):
        new_node = Node(name, phone_number)
        new_node.left = self.NIL
        new_node.right = self.NIL

        parent = None
        current = self.root

        while current != self.NIL:
            parent = current
            if new_node.name < current.name:
                current = current.left
            else:
                current = current.right

        new_node.parent = parent

        if parent is None:
            self.root = new_node
        elif new_node.name < parent.name:
            parent.left = new_node
        else:
            parent.right = new_node

        new_node.color = 'red'
        self.fix_insert(new_node)

    def left_rotate(self, x):
        y = x.right
        x.right = y.left
        if y.left != self.NIL:
            y.left.parent = x
        y.parent = x.parent

        if x.parent is None:
            self.root = y
        elif x == x.parent.left:
            x.parent.left = y
        else:
            x.parent.right = y

        y.left = x
        x.parent = y

    def right_rotate(self, y):
        x = y.left
        y.left = x.right
        if x.right != self.NIL:
            x.right.parent = y
        x.parent = y.parent

        if y.parent is None:
            self.root = x
        elif y == y.parent.right:
            y.parent.right = x
        else:
            y.parent.left = x

        x.right = y
        y.parent = x

    def fix_insert(self, k):
        while k.parent and k.parent.color == 'red':
            if k.parent == k.parent.parent.left:
                u = k.parent.parent.right
                if u.color == 'red':  # Case 1
                    k.parent.color = 'black'
                    u.color = 'black'
                    k.parent.parent.color = 'red'
                    k = k.parent.parent
                else:
                    if k == k.parent.right:  # Case 2
                        k = k.parent
                        self.left_rotate(k)
                    k.parent.color = 'black'  # Case 3
                    k.parent.parent.color = 'red'
                    self.right_rotate(k.parent.parent)
            else:
                u = k.parent.parent.left
                if u.color == 'red':  # Mirror Case 1
                    k.parent.color = 'black'
                    u.color = 'black'
                    k.parent.parent.color = 'red'
                    k = k.parent.parent
                else:
                    if k == k.parent.left:  # Mirror Case 2
                        k = k.parent
                        self.right_rotate(k)
                    k.parent.color = 'black'  # Mirror Case 3
                    k.parent.parent.color = 'red'
                    self.left_rotate(k.parent.parent)

        self.root.color = 'black'

    def inorder_traversal(self, node):
        if node != self.NIL:
            self.inorder_traversal(node.left)
            print(f"{node.name} - {node.phone_number} ({node.color})")
            self.inorder_traversal(node.right)

    def find_contact(self, name):
        current = self.root
        while current != self.NIL:
            if name == current.name:
                return current.phone_number
            elif name < current.name:
                current = current.left
            else:
                current = current.right
        return None

# === Example Usage ===
if __name__ == "__main__":
    phone_book = RedBlackTree()
    contacts = [
        ("Alice", "555-1234"),
        ("Bob", "555-5678"),
        ("Charlie", "555-2468"),
        ("Diana", "555-1357"),
        ("Eve", "555-9876")
    ]

    for name, phone in contacts:
        phone_book.insert(name, phone)

    print("Phone Directory (Alphabetical):")
    phone_book.inorder_traversal(phone_book.root)

    search_name = "Charlie"
    found = phone_book.find_contact(search_name)
    if found:
        print(f"\nPhone number for {search_name}: {found}")
    else:
        print(f"\n{search_name} not found in directory.")
