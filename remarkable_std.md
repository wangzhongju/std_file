# Remarkable Markdown Editor
Paragraphs are separated by a blank line
2nd paragraph.*ltalic*,**blod**,***both*** and `monospace`.

Here  is a bullet list:

- apple
- banana
- orange

Here is a numbered list:

1.apple
2.banana
3.orange

> Block quotes are writen like so.
> They can span multiple paragraphs, if you like

You can specify an inline MathJax equation like so:
$$x^2+y^2 = z^2$$

Or a block level equation with two dollar signs on  both sides of the eqution:

$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$

# cs201:Linked Lists
**Linked Lists** are a collection of **nodes** that are **linked together**. The *head* node refers to the start of the list.

![Linked Lists](LinkedList.png "Linked Lists")

Each **Node** contains some **data** and a **link to the next Node**

~~~java
publish class Node
{
	Node next;
	int data;
}
~~~

Linked Lists are **efficient** data structures **for adding new elements**

~~~java
public class LinkedList
{
	private Node head;
	
	public LinkedList(Node head) 
	{
		this.head = head;
	}
	
	public LinkedList(int data)
	{
		Node newNode = new Node(data);
		this.head = newNode;
	}
	
	public void add(int data) 
	{
		Node newNode = new Node(data);
		Node current = head;
		Node previous;
		
		while(current != null) 
		{
			previous = current;
			current = current.next;
		}
		
		previous.next = newNode;
	}
}