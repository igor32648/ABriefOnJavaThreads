# First Thread and jConsole

In Java by default, a classic "public static void main(String[] args)" method uses a thread to be executed.

You can use the Thread class to manipulate the current thread.

![Image 1](assets/image1.png)

The sleep method causes the currently executing thread to temporarily cease
execution according to the specified amount of time.

JDK includes a graphical tool for monitoring java local and remote machines that is called jConsole. You can view information about the running threads there. To access it open the terminal application and type the command “jconsole”.

![Image 2](assets/image2.png)

jConsole first page:

![Image 3](assets/image3.png)

Access jConsole while the FristThread project is running on Eclipse.

![Image 4](assets/image4.png)

There is a tab to view information about threads.

![Image 5](assets/image5.png)

Here you can see all current threads.

![Image 6](assets/image6.png)

You can see our main thread.

![Image 7](assets/image7.png)

# A simple divider using Threads

Create and run a simple java divider using Java Swing:
```
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class DivisionWindow {
    private JFrame frame;
    private JLabel num1Label, num2Label, resultLabel;
    private JTextField num1Field, num2Field;
    private JButton divideButton;

    public DivisionWindow() {
        frame = new JFrame("Division Window");
        frame.setSize(400, 200);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        Container container = frame.getContentPane();
        container.setLayout(new GridLayout(4, 2));

        num1Label = new JLabel("Enter Number 1: ");
        num1Field = new JTextField();
        num2Label = new JLabel("Enter Number 2: ");
        num2Field = new JTextField();
        resultLabel = new JLabel();

        divideButton = new JButton("Divide");
        divideButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int num1 = Integer.parseInt(num1Field.getText());
                int num2 = Integer.parseInt(num2Field.getText());
                int result = num1 / num2;
                resultLabel.setText("Result: " + result);
            }
        });

        container.add(num1Label);
        container.add(num1Field);
        container.add(num2Label);
        container.add(num2Field);
        container.add(divideButton);
        container.add(resultLabel);

        frame.setVisible(true);
    }

    public static void main(String[] args) {
        new DivisionWindow();
    }
}
```

![Image 8](assets/image8.png)

This is a simple calculator that can only divide two numbers. We can increase its performance by delegating each division that is made to a new thread. Create a Thread object, pass the dividing function in its constructor and then call the start() function. 

```
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class DivisionWindow {
    private JFrame frame;
    private JLabel num1Label, num2Label, resultLabel;
    private JTextField num1Field, num2Field;
    private JButton divideButton;

    public DivisionWindow() {
        frame = new JFrame("Division Window");
        frame.setSize(400, 200);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        Container container = frame.getContentPane();
        container.setLayout(new GridLayout(4, 2));

        num1Label = new JLabel("Enter Number 1: ");
        num1Field = new JTextField();
        num2Label = new JLabel("Enter Number 2: ");
        num2Field = new JTextField();
        resultLabel = new JLabel();

        divideButton = new JButton("Divide");
        divideButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                Thread divideThread = new Thread(() -> {
                    int num1 = Integer.parseInt(num1Field.getText());
                    int num2 = Integer.parseInt(num2Field.getText());
                    int result = num1 / num2;
                    resultLabel.setText("Result: " + result);
                });
                divideThread.start();
            }
        });

        container.add(num1Label);
        container.add(num1Field);
        container.add(num2Label);
        container.add(num2Field);
        container.add(divideButton);
        container.add(resultLabel);

        frame.setVisible(true);
    }

    public static void main(String[] args) {
        new DivisionWindow();
    }
}
```
# Using more than one thread at once

The code below creates two methods and then executes both using one thread each.

```
package market;

public class Market {

	public void justLooking(String name) {
		System.out.println(name + " has arrived");
			System.out.println(name + " is enterig market");
			System.out.println(name + " is going through shopping carts without picking one up");
			System.out.println(name + " is walking around");

			try {
				Thread.sleep(10000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

			System.out.println(name + " is checking prices");
			System.out.println(name + " is leaving");
	}

	public void buying(String name) {
		System.out.println(name + " has arrived");
			System.out.println(name + " is enterig market");
			System.out.println(name + " is picking up a shopping cart");
			System.out.println(name + " is walking around");
			System.out.println(name + " is checking prices");

			try {
				Thread.sleep(30000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

			System.out.println(name + " is picking up things to buy");
			System.out.println(name + " is paying");
			System.out.println(name + " is leaving");
	}

	public static void main(String[] args) {
		Market market = new Market();
		Thread client1Thread = new Thread(() -> {
			String name = Thread.currentThread().getName();
			market.justLooking(name);
		}, "Sandra");

		Thread client2Thread = new Thread(() -> {
			String name = Thread.currentThread().getName();
			market.buying(name);
		}, "Paulo");
		client1Thread.start();
		client2Thread.start();
	}
}
```
The console output should be like this:
```
Sandra has arrived
Sandra is enterig market
Paulo has arrived
Paulo is enterig market
Sandra is going through shopping carts without picking one up
Paulo is picking up a shopping cart
Paulo is walking around
Sandra is walking around
Paulo is checking prices
Sandra is checking prices
Sandra is leaving
Paulo is picking up things to buy
Paulo is paying
Paulo is leaving

```
Notice that both methods run at the same time. This behavior can be beneficial for your software by increasing its performance and decreasing the chances of it crashing.

But beware!

In our specific example, the two threads were created in a very simple way and are manipulating the same object (market) at the same time. This is not a good practice as it can lead to errors in the execution of the methods.

# Synchronizing Threads

We can solve the "multiple threads for 1 object" problem with the keyword "synchronized", like this:
```
package market;

public class Market {

	public void justLooking(String name) {
		System.out.println(name + " has arrived");
		synchronized (this) {
			System.out.println(name + " is enterig market");
			System.out.println(name + " is going through shopping carts without picking one up");
			System.out.println(name + " is walking around");

			try {
				Thread.sleep(10000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

			System.out.println(name + " is checking prices");
			System.out.println(name + " is leaving");
		}
	}

	public void buying(String name) {
		System.out.println(name + " has arrived");
		synchronized (this) {
			System.out.println(name + " is enterig market");
			System.out.println(name + " is picking up a shopping cart");
			System.out.println(name + " is walking around");
			System.out.println(name + " is checking prices");

			try {
				Thread.sleep(30000);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

			System.out.println(name + " is picking up things to buy");
			System.out.println(name + " is paying");
			System.out.println(name + " is leaving");
		}
	}

	public static void main(String[] args) {
		Market market = new Market();
		Thread client1Thread = new Thread(() -> {
			String name = Thread.currentThread().getName();
			market.justLooking(name);
		}, "Sandra");

		Thread client2Thread = new Thread(() -> {
			String name = Thread.currentThread().getName();
			market.buying(name);
		}, "Paulo");
		client1Thread.start();
		client2Thread.start();
	}
}
```
The console outcome:
```
Sandra has arrived
Sandra is enterig market
Paulo has arrived
Sandra is going through shopping carts without picking one up
Sandra is walking around
Sandra is checking prices
Sandra is leaving
Paulo is enterig market
Paulo is picking up a shopping cart
Paulo is walking around
Paulo is checking prices
```
Notice that apart from the phrase "Paulo has arrived", which was not synchronized, the rest of the second trhead only runs when the execution of the first one ends.


