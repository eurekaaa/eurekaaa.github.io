---
layout: post
title:  "Binary Search Tree In Javascript"
date:   2016-1-4 11:15:00 +0900
categories: jekyll update
---

Binary Search Tree

{% highlight javascript %}

function BinarySearchTree() {
    var Node = function(key) {
        this.key = key;
        this.left = null;
        this.right = null;
    };

    var root = null;

    /**
     * 노드 삽입
     * @param  {[type]} key [description]
     * @return {[type]}     [description]
     */
    var insertNode = function(node, newNode) {
        if(newNode.key < node.key) {
            if(node.left === null) {
                node.left = newNode;
            } else {
                insertNode(node.left, newNode);
            }
        } else {
            if(node.right === null) {
                node.right = newNode;
            } else {
                insertNode(node.right, newNode);
            }
        }
    };

    this.insert = function(key) {
        var newNode = new Node(key);

        if(root === null) {
            root = newNode;
        } else {
            insertNode(root, newNode);
        }
    };

    /**
     * 중위 순회
     * @param  {Function} callback [description]
     * @return {[type]}            [description]
     */
    var inOrderTraverseNode = function(node, callback) {
        if(node !== null) {
            inOrderTraverseNode(node.left, callback);
            callback(node.key);
            inOrderTraverseNode(node.right, callback);
        }
    }
    
    this.inOrderTraverse = function(callback) {
        inOrderTraverseNode(root, callback);
    }

    /**
     * 전위 순회
     * @param  {Function} callback [description]
     * @return {[type]}            [description]
     */
    var preOrderTraverseNode = function(node, callback) {
        if(node !== null) {
            callback(node.key);
            preOrderTraverseNode(node.left, callback);
            preOrderTraverseNode(node.right, callback);
        }
    }

    this.preOrderTraverse = function(callback) {
        preOrderTraverseNode(root, callback);
    }

    /**
     * 후위 순회
     * @param  {Function} callback [description]
     * @return {[type]}            [description]
     */
    var postOrderTraverseNode = function(node, callback) {
        if(node !== null) {
            postOrderTraverseNode(node.left, callback);
            postOrderTraverseNode(node.right, callback);
            callback(node.key);
        }
    }

    this.postOrderTraverse = function(callback) {
        postOrderTraverseNode(root, callback);
    }

    /**
     * 최소값 찾기
     * @return {[type]} [description]
     */
    var minNode = function(node) {
        if (node) {
            while (node && node.left !== null) {
                node = node.left;
            }

            return node.key;
        }

        return null;
    }

    this.min = function() {
        return minNode(root);
    }

    /**
     * 최대값 찾기
     * @param  {[type]} node [description]
     * @return {[type]}      [description]
     */
    var maxNode = function(node) {
        if (node) {
            while (node && node.right !== null) {
                node = node.right;
            }

            return node.key;
        }

        return null;
    }
    
    this.max = function() {
        return maxNode(root);
    }

    /**
     * key를 찾는다.
     * 있으면 true, 없으면 false를 리턴한다.
     * @param  {[type]} node [description]
     * @param  {[type]} key  [description]
     * @return {[type]}      [description]
     */
    var searchNode = function(node, key) {
        if (node === null) {
            return false;
        }

        if (key < node.key) {
            return searchNode(node.left, key);
        } else if (key > node.key) {
            return searchNode(node.right, key);
        } else {
            return true;
        }
    }

    this.search = function(key) {
        return searchNode(root, key);
    }

    var findMinNode = function(node) {
        if (node) {
            while (node && node.left !== null) {
                node = node.left;
            }

            return node;
        }
    }

    var removeNode = function(node, key) {
        if (node === null) {
            return null;
        }

        if (key < node.key) {
            node.left = removeNode(node.left, key);
            return node;
        } else if (key > node.key) {
            node.right = removeNode(node.right, key);
            return node;
        } else { // key가 node.key와 같다.

            // leaf node 인 경우
            if (node.left === null && node.right === null) {
                node = null;
                return node; // 부모가 가리키는 참조에 null을 넣기위해 node에 null을 대입하고 리턴한다.
            }

            // 오른쪽 자식만 있는 경우
            if (node.left === null) {
                node = node.right;
                return node;
            } else { // 왼쪽 자식만 있는 경우
                node = node.left;
                return node;
            }

            // 자식이 둘인 경우
            var aux = findMinNode(node.right); // 오른쪽 노드에서 최소값을 가진 노드를 가져온다.
            node.key = aux.key;
            node.right = removeNode(node.right, aux.key);
            return node;
        }
    }

    this.remove = function(key) {
        removeNode(root, key);
    }
}

var tree = new BinarySearchTree();
tree.insert(7);
tree.insert(15);
tree.insert(5);
tree.insert(3);
tree.insert(9);
tree.insert(8);
tree.insert(10);
tree.insert(13);
tree.insert(12);
tree.insert(14);
tree.insert(20);
tree.insert(18);
tree.insert(25);

function printNode(value) {
    console.log(value);
}
console.log('_________ inOrderTraverse');
tree.inOrderTraverse(printNode);
console.log('_________ preOrderTraverse');
tree.preOrderTraverse(printNode);
console.log('_________ postOrderTraverse');
tree.postOrderTraverse(printNode);

console.log('min value is ' + tree.min());
console.log('max value is ' + tree.max());
console.log('do you have 23? ' + tree.search(25));

tree.remove(25);
tree.remove(3);
tree.inOrderTraverse(printNode);

{% endhighlight %}