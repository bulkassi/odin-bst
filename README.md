# odin-bst

This repository presents an answer to the [Project: Binary Search Trees](https://www.theodinproject.com/lessons/javascript-linked-lists) task - to implement a binary search tree (BST) data structure using JavaScript.

> [!NOTE]
> This solution requires refinement, with some of the implemented functions being suboptimal.
> 
> For example, the `rebalance` function goes for a much easier approach - rebalancing the tree by creating a balanced tree using elements of the current one with in-order tree walk - its space complexity is $$O(n)$$ for storing an array of $$n$$ elements and creating a new tree. However, there are algorithms for rebalancing the BST in $$O(1)$$ space complexity and the same running time $$O(n)$$.

The full code snippet implementing the data structure is presented below.
```js
function createNode(valueData, leftNode = null, rightNode = null) {
  let value = valueData; 
  let left = leftNode || null;
  let right = rightNode || null;
    
  return {
    value,
    left,
    right,
  }
}

function createTree(numsArray) { 
  function buildTree(array) {
    if (array.length === 0) {
      return null;
    }
    
    const middle = Math.floor(array.length / 2);
    const rootNode = createNode(array[middle]);
    rootNode.left = buildTree(array.slice(0, middle));
    rootNode.right = buildTree(array.slice(middle + 1));
    
    return rootNode;
  }
  
  function find(root, value) {
    if (root === null) {
      return null;
    }
    
    if (root.value === value) {
      return root;
    }
    
    if (value < root.value) {
      return find(root.left, value);
    } else {
      return find(root.right, value);
    }
  }
  
  function inOrderTreeWalk(root, callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
  
    if (root === null) {
      return;
    }
    
    inOrderTreeWalk(root.left, callback);
    callback(root);
    inOrderTreeWalk(root.right, callback);
  }
  
  function preOrderTreeWalk(root, callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
  
    if (root === null) {
      return;
    }
    
    callback(root);
    preOrderTreeWalk(root.left, callback);
    preOrderTreeWalk(root.right, callback);
  }
  
  function postOrderTreeWalk(root, callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
  
    if (root === null) {
      return;
    }
    
    postOrderTreeWalk(root.left, callback);
    postOrderTreeWalk(root.right, callback);
    callback(root);
  }
  
  function inOrderForEach(callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
    
    if (root === null) {
      return;
    }
    
    inOrderTreeWalk(root, callback);
  }
  
  function preOrderForEach(callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
    
    if (root === null) {
      return;
    }
    
    preOrderTreeWalk(root, callback);
  }
  
  function postOrderForEach(callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
    
    if (root === null) {
      return;
    }
    
    postOrderTreeWalk(root, callback);
  }
  
  function levelOrderForEach(callback) {
    if (typeof callback !== 'function') {
      throw new Error('Callback function is required');
    }
    
    if (root === null) {
      return;
    }
    
    const queue = [root];
    
    while (queue.length > 0) {
      const queueFront = queue.shift();
      callback(queueFront);
      
      if (queueFront.left !== null) {
        queue.push(queueFront.left);
      }
      if (queueFront.right !== null) {
        queue.push(queueFront.right);
      }
    }
  }
  
  function search(rootNode, value) {
    if (rootNode === null || rootNode.value === value) {
      return rootNode;
    }
    if (value < rootNode.value) {
      return search(rootNode.left, value);
    }
    else {
      return search(rootNode.right, value);
    }
  }
  
  function calculateHeight(node) {
    if (node === null) {
      return -1; 
    }
  
    const leftHeight = calculateHeight(node.left);
    const rightHeight = calculateHeight(node.right);
    return Math.max(leftHeight, rightHeight) + 1;
  }
  
  function height(root, value) {
    const targetNode = search(root, value);
    if (targetNode === null) {
      return null;
    }
    return calculateHeight(targetNode);
  }
  
  function depth(root, value) {
    if (root === null) {
      return null;
    }
  
    // Helper function to find the depth recursively
    function findDepth(node, value, currentDepth) {
      if (node === null) {
        return null;
      }
    
      if (node.value === value) {
        return currentDepth;
      }
    
      // Search in left subtree
      const leftResult = findDepth(node.left, value, currentDepth + 1);
      if (leftResult !== null) {
        return leftResult;
      }
    
      // Search in right subtree
      const rightResult = findDepth(node.right, value, currentDepth + 1);
      return rightResult;
    }
  
    return findDepth(root, value, 0);
  }
  
  function isBalanced(root) {
    if (root === null) {
      return true;
    }
  
    // Helper function that returns an object with balance status and height
    function checkBalance(node) {
      // Base case: empty node is balanced with height -1
      if (node === null) {
        return { isBalanced: true, height: -1 };
      }  

      const leftResult = checkBalance(node.left);
      if (!leftResult.isBalanced) {
        return { isBalanced: false, height: 0 };
      }
    
      const rightResult = checkBalance(node.right);
      if (!rightResult.isBalanced) {
        return { isBalanced: false, height: 0 };
      }
    
      const heightDiff = Math.abs(leftResult.height - rightResult.height);
      const isCurrentBalanced = heightDiff <= 1;
    
      const currentHeight = Math.max(leftResult.height, rightResult.height) + 1;
    
      return {
        isBalanced: isCurrentBalanced,
        height: currentHeight
      };
    }

    return checkBalance(root).isBalanced;
  }
  
  function rebalance() {
    let numsArray = []
    
    inOrderForEach(function pushToArray(node) {
      numsArray.push(node.value);
    });
    
    root = buildTree(numsArray);
  }
  
  function insert(root, value) {
    if (root === null) {
      return createNode(value);
    }
  
    if (value < root.value) {
      root.left = insert(root.left, value);
    } else if (value > root.value) {
      root.right = insert(root.right, value);
    }
    // If value equals current node's value, we don't insert (no duplicates)
  
    return root;
  }

  function deleteItem(root, value) {
    if (root === null) {
      return null;
    }
  
    if (value < root.value) {
      // Value is in the left subtree
      root.left = deleteItem(root.left, value);
    } else if (value > root.value) {
      // Value is in the right subtree
      root.right = deleteItem(root.right, value);
    } else {
      // Found the node to delete
      // Case 1: Node has no children (leaf node)
      if (root.left === null && root.right === null) {
        return null;
      }
      // Case 2: Node has one child
      else if (root.left === null) {
        return root.right;
      } else if (root.right === null) {
        return root.left;
      }
      // Case 3: Node has two children
      else {
        let successor = root.right;
        while (successor.left !== null) {
          successor = successor.left;
        }
      
        root.value = successor.value;
      
        root.right = deleteItem(root.right, successor.value);
      }
    }
  
    return root;
  }
  
  let root = buildTree(numsArray.sort((a, b) => a - b)); 
  
  return {
    getRoot: () => root,
    prettyPrint,
    levelOrderForEach,
    inOrderForEach,
    preOrderForEach,
    postOrderForEach,
    search,
    height,
    depth,
    insert: (value) => {
      root = insert(root, value);
    },
    deleteItem: (value) => {
      root = deleteItem(root, value);
    },
    find: (value) => find(root, value),
    isBalanced: () => isBalanced(root),
    rebalance: () => rebalance()
  }
}
```
