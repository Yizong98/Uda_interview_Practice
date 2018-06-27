# Q1
def question1(s, t):
    """
    Given two strings s and t, determine whether some anagram of t
    is a substring of s.
    parameters: s: word checked against, t: word to check against
    output: Boolean

    doctests:
    >>> question1("udacity", "ad")
    True
    >>> question1("abcd", "abcd")
    True
    >>> question1("ad", "udacity")
    False

    """
    # count number of letters, store in dictionary
    def letter_count(item):
        item_count = {}
        for i in item:
            item_count[i] = item_count.get(i, 0) + 1
        return item_count
    # convert all letters to lower cases
    item_t = letter_count(t.lower())
    item_s = letter_count(s.lower())
    # loop through the word and check invalid cases
    for _t in item_t.keys():
        if _t not in item_s.keys():
            return False
        elif item_t[_t] > item_s[_t]:
            return False
    return True

# Q2


def question2(a):
    """
    Given a string a, find the longcest palindromic substring contained in a.
    parameters: a: word string
    output: palindromic string

    doctests:
    >>> question2("a")
    'a'
    >>> question2("abcbaiojadoijaosdj")
    'abcba'
    >>> question2("racecar")
    'racecar'
    """

    # convert all string letters to lower cases
    a = a.lower()
    substr = []
    for i in range(len(a)):
        for j in range(0, i):
            piece = a[j:i + 1]
            # compare original with backwards words
            if piece == piece[::-1]:
                substr.append(piece)
    if substr:
        return max(substr, key=len)
    elif len(a) == 1:
        return a
    return "nothing found"

# Q3


def question3(G):
    """
    Find the minimum spanning tree of a weighted undirected graph
    parameters: G: undirected graph
    output: output minimum spanning tree in form of list 

    doctests:
    >>> G = {'A': [('B', 2)],
    ...     'B': [('A', 2), ('C', 5)],
    ...     'C': [('B', 5)]}
    >>> G == question3(G)
    True
    >>> G = {'A': [('B', 7), ('D', 5)],
    ...     'B': [('A', 7), ('C', 8), ('D', 9), ('E', 7)],
    ...     'C': [('B', 8), ('E', 5)],
    ...     'D': [('A', 5), ('B', 9), ('E', 15), ('F', 6)],
    ...     'E': [('B', 7), ('C', 5), ('D', 15), ('F', 8), ('G', 9)],
    ...     'F': [('D', 6), ('E', 8), ('G', 11)],
    ...     'G': [('E', 9), ('F', 11)]}
    >>> H = {'A': [('D', 5), ('B', 7)],
    ...     'B': [('A', 7), ('E', 7)],
    ...     'C': [('E', 5)],
    ...     'D': [('A', 5), ('F', 6)],
    ...     'E': [('C', 5), ('B', 7), ('G', 9)],
    ...     'F': [('D', 6)],
    ...     'G': [('E', 9)]}

    >>> H == question3(G)
    True
    """
    # make sure G is dictionary
    assert isinstance(G, dict), "G is not dictionary!"

    # make sure G have more than one node
    assert(len(G) > 2), "G has not enough vertices to form edges!"

    # make edges a unique set and get nodes
    edges = set()
    nodes = G.keys()
    for n in nodes:
        for i in G[n]:
            if n > i[0]:
                edges.add((i[1], i[0], n))
            elif n < i[0]:
                edges.add((i[1], n, i[0]))
    edges = sorted(list(edges))
    # get unique nodes
    unique_nodes = [set(node) for node in nodes]
    processed_edges = []
    # loop through edges and compare
    for edge in edges:
        for j in range(len(unique_nodes)):
            if edge[1] in unique_nodes[j]:
                edge_1 = j
            if edge[2] in unique_nodes[j]:
                edge_2 = j
        if edge_1 > edge_2:
            unique_nodes[edge_2] = set.union(
                unique_nodes[edge_1], unique_nodes[edge_2])
            unique_nodes.pop(edge_1)
        elif edge_2 > edge_1:
            unique_nodes[edge_1] = set.union(
                unique_nodes[edge_1], unique_nodes[edge_2])
            unique_nodes.pop(edge_2)
        if edge_1 != edge_2:
            processed_edges.append(edge)
        if (len(unique_nodes) - 1) == 0:
            break
    # helper function for outputting graph

    def output_helper(edges):
        final_graph = {}
        for edge in edges:
            if edge[1] in final_graph:
                final_graph[edge[1]].append((edge[2], edge[0]))
            else:
                final_graph[edge[1]] = [(edge[2], edge[0])]
            if edge[2] in final_graph:
                final_graph[edge[2]].append((edge[1], edge[0]))
            else:
                final_graph[edge[2]] = [(edge[1], edge[0])]
        return final_graph
    return output_helper(processed_edges)
# Q4


def question4(T, r, n1, n2):
    """
    Find the least common ancestor between two nodes on a binary search tree.
    parameters: T: BST r: root n1,n2: two arbitrary nodes
    output: the LCA index

    doctest:
    >>> question4([[0, 1, 0, 0, 0],
    ...            [0, 0, 0, 0, 0],
    ...            [0, 0, 0, 0, 0],
    ...            [1, 0, 0, 0, 1],
    ...            [0, 0, 0, 0, 0]],
    ...             3,
    ...             1,4) == 3
    True
    >>> question4([
    ... [0,0,0,0,0,0],
    ... [1,0,0,0,0,0],
    ... [0,0,0,0,0,0],
    ... [0,1,0,0,1,0],
    ... [0,0,1,0,0,1],
    ... [0,0,0,0,0,0]
    ... ],
    ... 3,
    ... 4,
    ... 5) == 4
    True
    >>> question4([
    ... [0,0,0,0,0,0],
    ... [1,0,0,0,0,0],
    ... [0,1,0,1,0,0],
    ... [0,0,0,0,0,0],
    ... [0,0,1,0,0,1],
    ... [0,0,0,0,0,0]],
    ... 4,
    ... 1,
    ... 3) == 2
    True
    """
    vertex_idx = r
    root_val = T[vertex_idx]
    # sanity check for n1, n2
    assert isinstance(n1, int)
    assert isinstance(n2, int)
    tem_checker = root_val
    # loop to check left and right child
    while find_children_helper(tem_checker)[0] != None or find_children_helper(tem_checker)[1] != None:
        if vertex_idx > n1 and vertex_idx > n2:
            vertex_idx = find_children_helper(tem_checker)[0]
            tem_checker = T[vertex_idx]
        elif vertex_idx < n1 and vertex_idx < n2:
            vertex_idx = find_children_helper(tem_checker)[-1]
            tem_checker = T[vertex_idx]
        else:
            return vertex_idx
    return vertex_idx

# helper function for a list of children


def find_children_helper(n):
    child = []
    count = 0
    for node in n:
        if node == 1:
            child.append(count)
        count += 1
    return child
# Q5


class Node(object):

    def __init__(self, data):
        self.data = data
        self.next = None


def question5(ll, m):
    """
    Find the element in a singly linked list that's m elements from the end.
    parameters: ll: linkedlist m: element index
    output: node's data

    doctest:
    >>> n1 = Node(1)
    >>> n2 = Node(2)
    >>> n3 = Node(3)
    >>> n4 = Node(4)
    >>> n5 = Node(5)
    >>> n1.next = n2
    >>> n2.next = n3
    >>> n3.next= n4
    >>> n4.next = n5
    >>> question5(n1, 3)
    3
    >>> question5(n1, 1)
    5
    >>> n1 = Node(1)
    >>> n2 = Node(2)
    >>> n3 = Node(3)
    >>> n4 = Node(4)
    >>> n5 = Node(5)
    >>> n6 = Node(6)
    >>> n7 = Node(7)
    >>> n8 = Node(8)
    >>> n9 = Node(9)
    >>> n10 = Node(10)
    >>> n1.next = n2
    >>> n2.next = n3
    >>> n3.next= n4
    >>> n4.next = n5
    >>> n5.next = n6
    >>> n6.next = n7
    >>> n7.next = n8
    >>> n8.next = n9
    >>> n9.next = n10
    >>> question5(n1, 1)
    10
    """
    assert m > 0 and isinstance(m, int), "index must be positive integer"
    ll_length = get_len_helper(ll)
    # sanity check on index and possible circular linkedlist
    assert (ll_length != -1), "Spot circular linklist"
    assert (ll_length > m), "m is an invalid number"
    current_vertex = ll
    # loop through to get the element of the index
    for i in range(ll_length - m):
        current_vertex = current_vertex.next
    return current_vertex.data

# a helper function to get length of a linkedlist


def get_len_helper(link):
    empty = None
    assert (link.data != empty)
    if link.next == empty:
        return 1
    link_len = 0
    link_temp = link
    link_next = link.next
    # check empty and circular linklist
    while link_temp != empty and link_temp != link_next:
        link_temp = link_temp.next
        if link_next != None:
            link_next = link_next.next
        if link_next != None:
            link_next = link_next.next
        link_len += 1
    if link_temp == None:
        return link_len
    else:
        return -1
