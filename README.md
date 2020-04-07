# providedCode

//provided code

/** OVERVIEW: An unchecked exception that indicates a collection
 * has no elements (Cardinality: 0).
 */
public class EmptyException extends RuntimeException {

    /**
     * EFFECTS: Default constructor.
     */
    public EmptyException() {
        super();
    }

    /**
     * EFFECTS: A constructor that takes exception info.
     */
    public EmptyException(String s) {
        super(s);
    }
}
// intset.java

import java.util.ArrayList;
import java.util.Iterator;
import java.util.NoSuchElementException;

/**
 * OVERVIEW: IntSets are unbounded, mutable sets of integers. A typical IntSet
 * is {x1,...,xn}.
 */
public class IntSet {

    /**
     * EFFECTS: Constructor. Initializes this to be empty.
     */
    public IntSet() {
        els = new ArrayList<>();
    }

    /**
     * MODIFIES: this
     *
     * EFFECTS: Adds x to the elements of this.
     */
    public void insert(int x) {
        if (getIndex(x) < 0) {
            els.add(x);
        }
    }

    /**
     * MODIFIES: this
     *
     * EFFECTS: Removes x from this.
     */
    public void remove(int x) {
        int i = getIndex(x);
        if (i < 0) {
            return;
        }
        els.set(i, els.get(els.size() - 1));
        els.remove(els.size() - 1);
    }

    /**
     * EFFECTS: Returns true if x is in this; else returns false.
     */
    public boolean isIn(int x) {
        return getIndex(x) >= 0;
    }

    /**
     * EFFECTS: Returns the cardinality of this.
     */
    public int size() {
        return els.size();
    }

    /**
     * EFFECTS: If this is empty throws EmptyException; else returns an
     * arbitrary element of this.
     */
    public int choose() throws EmptyException {
        if (els.isEmpty()) {
            throw new EmptyException("IntSet.choose");
        }
        return els.get(0);
    }

    /**
     * EFFECTS: Returns an iterator that produces all the elements of this (as
     * Integers), each exactly once, in arbitrary order.
     *
     * REQUIRES: this must not be modified while the iterator is in use.
     */
    public Iterator<Integer> elements() {
        return els.iterator();
    }

    /**
     * EFFECTS: Returns an iterator that produces all the elements of this (as
     * Integers), each exactly once, in arbitrary order.
     *
     * REQUIRES: this must not be modified while the iterator is in use.
     */
    public Iterator<Integer> elements2() {
        return new IntSetGen(this);
    }

    // inner class 
    private static class IntSetGen implements Iterator<Integer> {

        IntSetGen(IntSet set) {
            // REQUIRES: set != null
            s = set;
            n = 0;
        }

        @Override
        public boolean hasNext() {
            return n < s.els.size();
        }

        @Override
        public Integer next() throws NoSuchElementException {
            if (hasNext()) {
                n++;
                return s.els.get(n - 1);
            } else {
                throw new NoSuchElementException("IntSet.elements");
            }
        }

        // unsupported
        @Override
        public void remove() {
            throw new UnsupportedOperationException("IntSet.elements");
        }

        // The abstraction function for IntSet1Gen is:
        //   AF(c) = [c.s.els[n], c.s.els[n + 1], ...]
        // The rep invariant for IntSet1Gen is 
        //   I(c) = c.s != null && (0 <= c.n <= c.s.size)
        private IntSet s; // the IntSet object being iterated
        private int n; // the index of the next element in els to return
    } // end IntSetGen 

    @Override
    public IntSet clone() {
        return new IntSet(els);
    }

    public boolean equals(IntSet s) {
        if (s == null || els.size() != s.els.size()) {
            return false;
        }

        for (var val : els) {
            if (s.els.indexOf(val) == -1) {
                return false;
            }
        }
        return true;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof IntSet)) {
            return false;
        }

        return equals((IntSet) o);
    }

    @Override
    public String toString() {
        if (els.isEmpty()) {
            return "IntSet: { }";
        }
        String s = "IntSet: {" + els.get(0);
        for (int i = 1; i < els.size(); i++) {
            s = s + ", " + els.get(i);
        }
        return s + "}";
    }

    public boolean repOk() {
        if (els == null) {
            return false;
        }
        for (int i = 0; i < els.size(); i++) {
            int x = els.get(i);
            for (int j = i + 1; j < els.size(); j++) {
                if (x == els.get(j)) {
                    return false;
                }
            }
        }
        return true;
    }

    /**
     * REQUIRES: v is not null.
     *
     * EFFECTS: Private Constructor. Initializes this to have the same elements
     * as those in v.
     */
    private IntSet(ArrayList<Integer> v) {
        els = new ArrayList<>();
        for (var val : v) {
            els.add(val);
        }
    }

    /**
     * EFFECTS: If x is in this returns the index where x appears; else returns
     * -1.
     */
    private int getIndex(Integer x) {
        for (int i = 0; i < els.size(); i++) {
            if (x.equals(els.get(i))) {
                return i;
            }
        }
        return -1;
    }

    // A typical IntSet is {x1,...,xn}.    
    // The abstraction function is
    //   AF(c) = { c.els[i] | 0 <= i < c.els.size }
    // The rep invariant is 
    //   I(c) = c.els != null &&
    //     for all int i, j, 0 < i, j < c.els.size &&
    //     i != j => c.els[i] != c.els[j] 
    private ArrayList<Integer> els; // the rep

    /**
     * Effects: Testing methods of the class IntSet
     */
    public static void main(String[] args) {
        int[] intArray = {0, 1, 2, 3};
        IntSet myIntSet = getElements(intArray);

        System.out.println("myIntSet's size is " + myIntSet.size());
        System.out.println("myIntSet is " + myIntSet);
        System.out.println("0 is in myIntSet: " + myIntSet.isIn(0));
        System.out.println("100 is in myIntSet: " + myIntSet.isIn(100));
        System.out.println();

        System.out.println("Removing 4 from myIntSet ...");
        myIntSet.remove(4);
        System.out.println("myIntSet's size is " + myIntSet.size());
        System.out.println("myIntSet is " + myIntSet);
        System.out.println();

        System.out.println("Removing 3 from myIntSet ...");
        myIntSet.remove(3);
        System.out.println("myIntSet's size is " + myIntSet.size());
        System.out.println("myIntSet is " + myIntSet);
        System.out.println();

        System.out.println("Inerting 1 to myIntSet ...");
        myIntSet.insert(1);
        System.out.println("myIntSet's size is " + myIntSet.size());
        System.out.println("myIntSet is " + myIntSet);
        System.out.println();

        System.out.println("Inerting -1 to myIntSet ...");
        myIntSet.insert(-1);
        System.out.println("myIntSet's size is " + myIntSet.size());
        System.out.println("myIntSet is " + myIntSet);
        System.out.println();

        System.out.println("Get a int value from myIntSet ...");
        int val = myIntSet.choose();
        System.out.println("What we get is " + val);
        System.out.println();

        System.out.println("Cloning myIntSet to myIntSet2 ...");
        var myIntSet2 = (IntSet) myIntSet.clone();
        System.out.println("myIntSet2's size is " + myIntSet2.size());
        System.out.println("myIntSet2 is " + myIntSet2);
        System.out.println("myIntSet2 == myIntSet: " + myIntSet2.equals(myIntSet));
        System.out.println();

        System.out.println("Creating myIntSet3 ...");
        int[] intArray3 = {3, 2, 0, 1};
        IntSet myIntSet3 = getElements(intArray3);
        System.out.println("myIntSet3's size is " + myIntSet3.size());
        System.out.println("myIntSet3 is " + myIntSet3);
        System.out.println("myIntSet3 == myIntSet: " + myIntSet3.equals(myIntSet));
        System.out.println();

        System.out.println("Creating myIntSet4 ...");
        IntSet myIntSet4 = getElements(intArray);
        System.out.println("myIntSet4's size is " + myIntSet4.size());
        System.out.println("myIntSet4 is " + myIntSet4);
        System.out.println("myIntSet3 == myIntSet4: " + myIntSet3.equals(myIntSet4));
        System.out.println();

        System.out.println("Iterating all the elements of myIntSet ...");
        var g = myIntSet.elements();
        while (g.hasNext()) {
            System.out.println(g.next() + " is in myIntSet.");
        }
        System.out.println();

        g = myIntSet.elements();
        System.out.println("The maximum element in myIntSet is " + max(g));
        System.out.println();
        
        System.out.println("Iterating all the elements of myIntSet3 ...");
        var g2 = myIntSet3.elements2();
        while (g2.hasNext()) {
            System.out.println(g2.next() + " is in myIntSet3.");
        }
        System.out.println();

        g2 = myIntSet3.elements2();
        System.out.println("The maximum element in myIntSet3 is " + max(g2));
        System.out.println();

        // code below tests sortedElements
        System.out.println();
        System.out.println("Iterating all the elements of myIntSet3 "
                + "in ascending order ...");
        var sg3 = myIntSet3.sortedElements();
        int prevElement, counter = 0;
        if (sg3.hasNext()) {
            prevElement = sg3.next();
            System.out.println(prevElement + " is in myIntSet3.");
            counter++;

            while (sg3.hasNext()) {
                int curElement = sg3.next();
                System.out.println(curElement + " is in myIntSet3.");
                if (prevElement < curElement) {
                    prevElement = curElement;
                    counter++;
                } else {
                    System.out.println("sortedElements does not work "
                            + "properly! Execution stopped!");
                    return;
                }
            }

            if (counter != myIntSet3.size()) {
                System.out.println("sortedElements does not work properly! It "
                        + "returns " + counter + " elements, while myIntSet3"
                        + " has " +  myIntSet3.size() + " elements. Execution"
                                + " stopped!");
                return;
            }
        } else {
            System.out.println("sortedElements does not work properly! "
                    + "myIntSet3 is not empty.  Execution stopped!");
            return;
        }
        System.out.println();

        System.out.println("Creating myIntSet5, which is empty ...");
        var myIntSet5 = new IntSet();
        System.out.println("myIntSet5's size is " + myIntSet5.size());
        var sg5 = myIntSet5.sortedElements();
        if (sg5.hasNext()) {
            System.out.println("sortedElements does not work properly! "
                    + "myIntSet5 is empty.  Execution stopped!");
            return;
        } else {
            System.out.println("sortedElements works properly for myIntSet5! ");
        }
        System.out.println();
        System.out.println("All tests completed.");        
    }

    /**
     * EFFECTS: if a is null throws NullPointerException; else returns a set
     * containing an entry for each distinct element of a.
     */
    public static IntSet getElements(int[] a) throws
            NullPointerException {
        IntSet s = new IntSet();
        for (int i = 0; i < a.length; i++) {
            s.insert(a[i]);
        }
        return s;
    }

    /**
     * Modifies: g 
     * 
     * EFFECTS: If g is null, throws NullPointerException; else if g
     * is empty, throws EmptyException; else returns the largest int in g.
     */
    public static int max(Iterator<Integer> g)
            throws EmptyException, NullPointerException {
        try {
            int m = g.next();
            while (g.hasNext()) {
                int x = g.next();
                if (m < x) {
                    m = x;
                }
            }
            return m;
        } catch (NoSuchElementException e) {
            throw new EmptyException("IntSet.max");
        }
    }
