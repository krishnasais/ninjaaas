import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;
import java.util.*;
import java.util.Map;
import java.util.List;

public class TrainBookingApp extends JFrame {
    private List<Train> trainList = new ArrayList<>();
    private Map<Integer, Map<String, Integer>> trainSeats = new HashMap<>();
    private List<TrainBooking> bookings = new ArrayList<>();
    private int bookingCounter = 1;
    private String[] departureLocations = {"Kakinada", "Vijayawada", "Samarlakota", "Visakhapatnam"};

    private String[] seatPreferences = {"General", "1st AC", "2nd AC", "3rd AC"};
    private String[] mealPreferences = {"No Meal", "Breakfast", "Lunch", "Dinner"};

    public TrainBookingApp() {
        setTitle("Train Booking System");
        setSize(400, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        try {
            UIManager.setLookAndFeel("javax.swing.plaf.nimbus.NimbusLookAndFeel");
        } catch (Exception e) {
            e.printStackTrace();
        }

        initTrainData();
        initCompartments();
        initUI();

        setVisible(true);
    }

    private void initTrainData() {
        trainList.add(new Train(101, "Kakinada", "08:00 AM"));
        trainList.add(new Train(102, "Vijayawada", "09:30 AM"));
        trainList.add(new Train(103, "Samarlakota", "11:15 AM"));
        trainList.add(new Train(104, "Visakhapatnam", "01:45 PM"));
    }
    private void initTrainDataFromDatabase() {
        String url = "jdbc:mysql://localhost:3306/TrainBookingApp";

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection connection = DriverManager.getConnection(url);
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("SELECT * FROM train");

            while (resultSet.next()) {
                int trainNo = resultSet.getInt("trainNo");
                String destination = resultSet.getString("destination");
                String timing = resultSet.getString("timing");
                trainList.add(new Train(trainNo, destination, timing));
            }

            connection.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void initCompartments() {
        for (Train train : trainList) {
            Map<String, Integer> compartments = new HashMap<>();
            for (String preference : seatPreferences) {
                compartments.put(preference, 18);
            }
            trainSeats.put(train.getTrainNo(), compartments);
        }
    }

    private void initUI() {
        JPanel buttonPanel = new JPanel(new GridLayout(7, 1));
        JButton bookButton = new JButton("Book a Train");
        JButton statusButton = new JButton("Check Booking Status");
        JButton cancelButton = new JButton("Cancel a Ticket");
        JButton reportButton = new JButton("Report a Problem");
        JButton availableSeatsButton = new JButton("Available Seats");
        JButton exitButton = new JButton("Exit");
        bookButton.setBackground(new Color(46, 139, 87));
        statusButton.setBackground(new Color(166, 255, 71));
        cancelButton.setBackground(new Color(237, 179, 92));
        reportButton.setBackground(new Color(237, 179, 92));
        exitButton.setBackground(new Color(204, 30, 20));
        availableSeatsButton.setBackground(new Color(166, 255, 71));
        bookButton.setForeground(Color.white);

        bookButton.addActionListener(e -> openBookingDialog());
        statusButton.addActionListener(e -> showBookingStatus());
        cancelButton.addActionListener(e -> openCancelDialog());
        reportButton.addActionListener(e -> openReportDialog());
        availableSeatsButton.addActionListener(e -> showAvailableSeats());
        exitButton.addActionListener(e -> System.exit(0));

        buttonPanel.add(bookButton);
        buttonPanel.add(statusButton);
        buttonPanel.add(availableSeatsButton);
        buttonPanel.add(cancelButton);
        buttonPanel.add(reportButton);
        buttonPanel.add(exitButton);

        add(buttonPanel);
    }

    private void openBookingDialog() {
        JDialog bookingDialog = new JDialog(this, "Book a Train");
        bookingDialog.setSize(400, 250);  // Adjusted size
        bookingDialog.setLayout(new GridLayout(8, 2));  // Adjusted rows and columns

        bookingDialog.add(new JLabel("Passenger Name:"));
        JTextField nameField = new JTextField();
        bookingDialog.add(nameField);

        bookingDialog.add(new JLabel("Aadhaar ID:"));
        JTextField aadhaarField = new JTextField();
        bookingDialog.add(aadhaarField);

        bookingDialog.add(new JLabel("Age:"));
        JTextField ageField = new JTextField();
        bookingDialog.add(ageField);

        JComboBox<String> trainComboBox = new JComboBox<>();
        for (Train train : trainList) {
            trainComboBox.addItem(train.getTrainNo() + " - " + train.getDestination());
        }
        bookingDialog.add(new JLabel("Select a Train:"));
        bookingDialog.add(trainComboBox);

        JComboBox<String> seatPreferenceComboBox = new JComboBox<>(seatPreferences);
        bookingDialog.add(new JLabel("Select Compartment Preference:"));
        bookingDialog.add(seatPreferenceComboBox);

        JComboBox<String> mealPreferenceComboBox = new JComboBox<>(mealPreferences);
        bookingDialog.add(new JLabel("Select Meal Preference:"));
        bookingDialog.add(mealPreferenceComboBox);

        JComboBox<String> fromLocationComboBox = new JComboBox<>(departureLocations);
        bookingDialog.add(new JLabel("Select Departure Location:"));
        bookingDialog.add(fromLocationComboBox);

        JButton bookButton = new JButton("Book");
        bookingDialog.add(bookButton);

        bookButton.addActionListener(e -> {
            String passengerName = nameField.getText();
            String aadhaarId = aadhaarField.getText();
            int age = Integer.parseInt(ageField.getText());
            int selectedTrainIndex = trainComboBox.getSelectedIndex();
            int selectedTrainNo = trainList.get(selectedTrainIndex).getTrainNo();
            String selectedPreference = seatPreferenceComboBox.getSelectedItem().toString();
            String selectedMealPreference = mealPreferenceComboBox.getSelectedItem().toString();
            String fromLocation = fromLocationComboBox.getSelectedItem().toString();

            Map<String, Integer> compartments = trainSeats.get(selectedTrainNo);
            int availableSeats = compartments.get(selectedPreference);

            if (availableSeats > 0) {
                compartments.put(selectedPreference, availableSeats - 1);
                int cost = calculateCost(selectedPreference, selectedMealPreference);
                bookings.add(new TrainBooking(selectedTrainNo, bookingCounter++, passengerName, aadhaarId, age, selectedPreference, cost, fromLocation));
                bookingDialog.dispose();
                showBookingStatus();
            } else {
                JOptionPane.showMessageDialog(bookingDialog, "No available seats in the selected compartment.");
            }
        });

        bookingDialog.setVisible(true);
    }


    // The rest of your code remains the same
    private void openCancelDialog() {
        JDialog cancelDialog = new JDialog(this, "Cancel Ticket");
        cancelDialog.setSize(300, 200);
        cancelDialog.setLayout(new GridLayout(3, 2));

        cancelDialog.add(new JLabel("Booking ID:"));
        JTextField bookingIdField = new JTextField();
        cancelDialog.add(bookingIdField);

        JButton cancelTicketButton = new JButton("Cancel Ticket");
        cancelDialog.add(cancelTicketButton);

        cancelTicketButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String bookingIdText = bookingIdField.getText();
                try {
                    int bookingId = Integer.parseInt(bookingIdText);
                    boolean canceled = cancelTicket(bookingId);
                    if (canceled) {
                        JOptionPane.showMessageDialog(cancelDialog, "Ticket canceled successfully.");
                    } else {
                        JOptionPane.showMessageDialog(cancelDialog, "Ticket not found with the provided Booking ID.");
                    }
                } catch (NumberFormatException ex) {
                    JOptionPane.showMessageDialog(cancelDialog, "Please enter a valid Booking ID.");
                }
            }
        });

        cancelDialog.setVisible(true);
    }

    private boolean cancelTicket(int bookingId) {
        for (TrainBooking booking : bookings) {
            if (booking.getBookingId() == bookingId) {
                bookings.remove(booking);
                return true;
            }
        }
        return false;
    }

    private void openReportDialog() {
        JDialog reportDialog = new JDialog(this, "Report a Problem");
        reportDialog.setSize(300, 200);
        reportDialog.setLayout(new GridLayout(3, 1));

        reportDialog.add(new JLabel("Please describe the problem:"));
        JTextArea problemTextArea = new JTextArea();
        reportDialog.add(new JScrollPane(problemTextArea));

        JButton submitReportButton = new JButton("Submit Report");
        reportDialog.add(submitReportButton);

        submitReportButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String problemDescription = problemTextArea.getText();
                // You can save the problem description to a file or send it to a server for further processing.
                JOptionPane.showMessageDialog(reportDialog, "Problem reported. Thank you!");
                reportDialog.dispose();
            }
        });

        reportDialog.setVisible(true);
    }

    private int calculateCost(String bookingClass, String mealPreference) {
        int baseCost = 0;
        switch (bookingClass) {
            case "General":
                baseCost = 500;
                break;
            case "1st AC":
                baseCost = 2000;
                break;
            case "2nd AC":
                baseCost = 1000;
                break;
            case "3rd AC":
                baseCost = 800;
                break;
        }

        if (!mealPreference.equals("No Meal")) {
            baseCost += 200;
        }

        return baseCost;
    }

    private void showAvailableSeats() {
        JDialog availableSeatsDialog = new JDialog(this, "Available Seats");
        availableSeatsDialog.setSize(300, 200);
        availableSeatsDialog.setLayout(new GridLayout(1, 1));

        JComboBox<String> trainComboBox = new JComboBox<>();
        for (Train train : trainList) {
            trainComboBox.addItem(train.getTrainNo() + " - " + train.getDestination());
        }
        availableSeatsDialog.add(trainComboBox);

        JTextArea availableSeatsArea = new JTextArea();
        availableSeatsArea.setEditable(false);
        availableSeatsDialog.add(new JScrollPane(availableSeatsArea));

        JButton showSeatsButton = new JButton("Show Seats");
        availableSeatsDialog.add(showSeatsButton);

        showSeatsButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int selectedTrainIndex = trainComboBox.getSelectedIndex();
                int selectedTrainNo = trainList.get(selectedTrainIndex).getTrainNo();
                Map<String, Integer> compartments = trainSeats.get(selectedTrainNo);

                availableSeatsArea.setText("Available Seats for Train " + selectedTrainNo + ":\n");
                for (String preference : seatPreferences) {
                    availableSeatsArea.append(preference + ": " + compartments.get(preference) + " available seats\n");
                }
            }
        });

        availableSeatsDialog.setVisible(true);
    }

    private void showBookingStatus() {
        JTextArea statusArea = new JTextArea();
        statusArea.setEditable(false);

        if (bookings.isEmpty()) {
            statusArea.append("No bookings found.");
        } else {
            statusArea.append("Booking Status:\n");
            for (TrainBooking booking : bookings) {
                statusArea.append("Booking ID: " + booking.getBookingId() +
                        ", Train No: " + booking.getTrainNo() +
                        ", Passenger Name: " + booking.getPassengerName() +
                        ", Compartment Preference: " + booking.getSeatPreference() +
                        ", Meal Preference: " + booking.getMealPreference() +
                        ", Cost: Rs" + booking.getCost() + "\n");
            }
        }

        JScrollPane scrollPane = new JScrollPane(statusArea);
        scrollPane.setPreferredSize(new Dimension(400, 300));

        JFrame statusFrame = new JFrame("Booking Status");
        statusFrame.add(scrollPane);
        statusFrame.pack();
        statusFrame.setVisible(true);
    }


    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new TrainBookingApp());
    }
}

class Train {
    private int trainNo;
    private String destination;
    private String timing;

    public Train(int trainNo, String destination, String timing) {
        this.trainNo = trainNo;
        this.destination = destination;
        this.timing = timing;
    }

    public int getTrainNo() {
        return trainNo;
    }

    public String getDestination() {
        return destination;
    }

    public String getTiming() {
        return timing;
    }
}

class TrainBooking {
    private int trainNo;
    private int bookingId;
    private String passengerName;
    private String aadhaarId;
    private int age;
    private String seatPreference;
    private String mealPreference;
    private int cost;
    private String fromLocation;

    public TrainBooking(int trainNo, int bookingId, String passengerName, String aadhaarId, int age, String seatPreference, int cost, String fromLocation) {
        this.trainNo = trainNo;
        this.bookingId = bookingId;
        this.passengerName = passengerName;
        this.aadhaarId = aadhaarId;
        this.age = age;
        this.seatPreference = seatPreference;
        this.fromLocation = fromLocation;
        this.cost = cost;
    }

    public String getFromLocation() {
        return fromLocation;
    }

    public int getTrainNo() {
        return trainNo;
    }

    public int getBookingId() {
        return bookingId;
    }

    public String getPassengerName() {
        return passengerName;
    }

    public String getSeatPreference() {
        return seatPreference;
    }

    public String getMealPreference() {
        return mealPreference;
    }

    public int getCost() {
        return cost;
    }
}
