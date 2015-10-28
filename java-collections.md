
## The interface hierarchy

- Collection
    - Set
        - SortedSet
    - List
    - Queue
    - Deque
- Map
    - SortedMap

---

## Collection

### Conversion constructor

```java
Set<String> set = new HashSet<String>();
...
List<String> list = new ArrayList<String>(set);
```

### Bulk operations

- containsAll
- addAll
- removeAll
- retainAll
- clear

### Array operations

```java
Object[] a = c.toArray(); // raw type

String[] a = c.toArray(new String[0]); // typed
```

---

## Set

- HashSet
    - A hash table
- TreeSet
    - A red-black tree.
    - Order its elements based on their values.
- LinkedHashSet
    - A hash table with a linked list running through it.
    - Order its elements based on the order in which they were inserted into the set.

### Bulk operations

- s1.containsAll(s2)
- s1.addAll(s2) // Union
- s1.retainAll(s2) // Intersection
- s1.removeAll(s2) // Asymetric difference

---

## Queue and Deque

Queue methods (returns null or false when queue is empty):

- offer(e)
- poll()
- peek()

Deque methods (used as a stack):

- push(e)
- pop()
- peek()

Deque can also be used a FIFO Queue.

---

## Map

Implementations:

- HashMap
- TreeMap
- LinkedHashMap

Bulk operations:

- clear
- putAll
- addAll

Collection views:

- keySet
- values
- entrySet
	- Set<Map.Entry>

Idioms to iterate over a Map:

```java
for (KeyType key : m.keySet())
	System.out.println(key);
```

```java
for (Iterator<KeyType> it = m.keySet().iterator(); it.hasNext(); )
	if (it.next().isBogus())
		it.remove();
```

```java
for (Map.Entry<KeyType, ValType> e : m.entrySet())
	System.out.println(e.getKey() + ": " + e.getValue());
```

---

## Object Ordering

```java
Collections.sort(l);
```

### The `Comparable` interface

```java
public interface Comparable<T> {
	int compareTo(T o);
}
```

An example:

```java
import java.util.*;

public class Name implements Comparable<Name> {
	private final String firstName, lastName;

	public Name(String f, String l) {
		if (f == null || l == null) {
			throw NullPointerException();
		}
		firstName = f;
		lastName = l;
	}

	public String firstName() {
		return f;
	}

	public String lastName() {
		return l;
	}

	@Override
	public boolean equals(Object o) {
		if (!(o instanceof Name)) {
			return false;
		}
		return o.firstName().equals(firstName) && o.lastName().equals(lastName);
	}

	@Override
	public int hashCode() {
		return firstName.hashCode() * 31 + lastName.hashCode();
	}

	@Override
	public String toString() {
		return firstName + " " + lastName;
	}

	@Override
	public int compareTo(Name o) {
		// last name is the most significant
		int lastComp = lastName.compareTo(o.lastName());
		return lastComp != 0 ? lastComp : firstName.compareTo(o.firstName());
	}
}
```

### The `Comparator` interface

```java
public interface Comparator<T> {
	int compare(T a, T b);
}
```

An example:

```java
public class Employee implements Comparable<Employee> {
    public Name name()     { ... }
    public int number()    { ... }
    public Date hireDate() { ... }
       ...
}

import java.util.*;

public class EmpSort {
	static final Comparator<Employee> SENIORITY_ORDER = new Comparator<Employee> {
		@Override
		public int compare(Employee a, Employee b) {
			// Don't tempt to do -a.hireDate().compareTo(b.hireDate())
			// `compareTo()` can return any negative int if its argument is
			// greater than the object on which it is invoked.
			int dateComp = b.hireDate().compareTo(a.hireDate());
			if (dateComp != 0) {
				return dateComp;
			}
			// Preserve the effect of `equals()`
			// 
			// Don't tempt to do a.number() - b.number() unless you are sure
			// they are both non-negative integers. Otherwise, integer overflow
			// may occur if we subtract a large negative integer from a large
			// positive integer.
			return a.number() == b.number() ? 0 : (a.number() < b.numner() ? -1 : 1);
		}
	};
}

```

```java
-Integer.MIN_VALUE == Integer.MIN_VALUE
```

> Always be careful about overflow problems when doing arithmetic operations on signed integers.

> Singed integer type is not big enough to represent the difference of two arbitrary signed integers. If i is a large positive integer and j is a large negative integer, i - j will overflow and will return a negative number.

---
