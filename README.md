import java.util.ArrayList;
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

// Класс автосалона
class CarShowroom {
    private String name;
    private ArrayList<Car> cars;

    public CarShowroom(String name) {
        this.name = name;
        this.cars = new ArrayList<>();
    }

    public String getName() {
        return name;
    }

    public void addCar(Car car) {
        cars.add(car);
    }

    public void removeCar(int index) {
        if (index >= 0 && index < cars.size()) {
            cars.remove(index);
        }
    }

    public Car getCar(int index) {
        return (index >= 0 && index < cars.size()) ? cars.get(index) : null;
    }

    public ArrayList<Car> getCars() {
        return cars;
    }
}

// Класс автомобиля
class Car {
    private String brandName;
    private int maxPassengers;
    private double cost;
    private int quantityInStock;
    private boolean availability;
    private ArrayList<PurchaseRequest> purchaseRequests;

    public Car(String brandName, int maxPassengers, double cost, int quantityInStock) {
        this.brandName = brandName;
        this.maxPassengers = maxPassengers;
        this.cost = cost;
        this.quantityInStock = quantityInStock;
        this.purchaseRequests = new ArrayList<>();
        updateAvailability();
    }

    public String getBrandName() {
        return brandName;
    }

    public void addRequest(PurchaseRequest request) {
        purchaseRequests.add(request);
        updateAvailability();
    }

    public ArrayList<PurchaseRequest> getRequests() {
        return purchaseRequests;
    }

    private void updateAvailability() {
        this.availability = quantityInStock > purchaseRequests.size();
    }

    public String toString() {
        return "Car: " + brandName + ", Cost: " + cost + ", Available: " + availability;
    }
}

// Класс заявки на покупку
class PurchaseRequest {
    private String buyerName;
    private String phoneNumber;
    private Car car;

    public PurchaseRequest(String buyerName, String phoneNumber, Car car) {
        this.buyerName = buyerName;
        this.phoneNumber = phoneNumber;
        this.car = car;
        car.addRequest(this);
    }

    public String toString() {
        return "Request: " + buyerName + " for " + car.getBrandName();
    }
}

// Графический интерфейс
class CarShowroomGUI extends JFrame {
    private CarShowroom showroom;
    private DefaultListModel<String> carListModel;
    private JList<String> carList;

    public CarShowroomGUI() {
        showroom = new CarShowroom("Luxury Cars");

        setTitle("Car Showroom");
        setSize(400, 300);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        carListModel = new DefaultListModel<>();
        carList = new JList<>(carListModel);
        add(new JScrollPane(carList), BorderLayout.CENTER);

        JPanel panel = new JPanel();
        panel.setLayout(new FlowLayout());

        JButton addCarButton = new JButton("Add Car");
        addCarButton.addActionListener(e -> addCar());
        panel.add(addCarButton);

        JButton removeCarButton = new JButton("Remove Car");
        removeCarButton.addActionListener(e -> removeCar());
        panel.add(removeCarButton);

        JButton showRequestsButton = new JButton("Show Requests");
        showRequestsButton.addActionListener(e -> showRequests());
        panel.add(showRequestsButton);

        JButton addRequestButton = new JButton("Add Purchase Request");
        addRequestButton.addActionListener(e -> addRequest());
        panel.add(addRequestButton);

        add(panel, BorderLayout.SOUTH);

        setVisible(true);
    }

    private void addCar() {
        String brand = JOptionPane.showInputDialog("Enter car brand:");
        String maxPassengers = JOptionPane.showInputDialog("Enter max passengers:");
        String cost = JOptionPane.showInputDialog("Enter cost:");
        String quantity = JOptionPane.showInputDialog("Enter quantity in stock:");

        if (brand != null && maxPassengers != null && cost != null && quantity != null) {
            Car car = new Car(brand, Integer.parseInt(maxPassengers), Double.parseDouble(cost), Integer.parseInt(quantity));
            showroom.addCar(car);
            carListModel.addElement(car.toString());
        }
    }

    private void removeCar() {
        int index = carList.getSelectedIndex();
        if (index != -1) {
            showroom.removeCar(index);
            carListModel.remove(index);
        }
    }

    private void showRequests() {
        int index = carList.getSelectedIndex();
        if (index != -1) {
            Car car = showroom.getCar(index);
            JOptionPane.showMessageDialog(this, car.getRequests().toString());
        }
    }

    private void addRequest() {
        int index = carList.getSelectedIndex();
        if (index != -1) {
            Car car = showroom.getCar(index);
            String buyer = JOptionPane.showInputDialog("Enter buyer name:");
            String phone = JOptionPane.showInputDialog("Enter phone number:");
            if (buyer != null && phone != null) {
                new PurchaseRequest(buyer, phone, car);
            }
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(CarShowroomGUI::new);
    }
}
