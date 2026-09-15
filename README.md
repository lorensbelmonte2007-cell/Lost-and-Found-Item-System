# Lost-and-Found-Item-System
STUDENT LOST AND FOUND SYSTEM - ALL JAVA CODE

FILE: Item.java

public abstract class Item {
    private int id;
    private String name;
    private String description;
    private String location;
    private String date;

    public Item(int id, String name, String description, String location, String date) {
        this.id = id;
        this.name = name;
        this.description = description;
        this.location = location;
        this.date = date;
    }

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public String getLocation() { return location; }
    public void setLocation(String location) { this.location = location; }

    public String getDate() { return date; }
    public void setDate(String date) { this.date = date; }

    public abstract String getType();

    @Override
    public String toString() {
        return "[" + getType() + "] ID: " + id
                + " | Item: " + name
                + " | Description: " + description
                + " | Location: " + location
                + " | Date: " + date;
    }
}

FILE: Student.java

public class Student {
    private String studentId;
    private String name;
    private String course;

    public Student(String studentId, String name, String course) {
        this.studentId = studentId;
        this.name = name;
        this.course = course;
    }

    public String getStudentId() { return studentId; }
    public void setStudentId(String studentId) { this.studentId = studentId; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getCourse() { return course; }
    public void setCourse(String course) { this.course = course; }

    @Override
    public String toString() {
        return name + " (" + studentId + ") - " + course;
    }
}

FILE: LostItem.java


public class LostItem extends Item {
    private Student owner;

    public LostItem(int id, String name, String description,
                    String location, String date, Student owner) {
        super(id, name, description, location, date);
        this.owner = owner;
    }

    public Student getOwner() { return owner; }
    public void setOwner(Student owner) { this.owner = owner; }

    @Override
    public String getType() { return "LOST"; }

    @Override
    public String toString() {
        return super.toString() + " | Owner: " + owner.getName();
    }
}


FILE: FoundItem.java


public class FoundItem extends Item {
    private Student finder;

    public FoundItem(int id, String name, String description,
                     String location, String date, Student finder) {
        super(id, name, description, location, date);
        this.finder = finder;
    }

    public Student getFinder() { return finder; }
    public void setFinder(Student finder) { this.finder = finder; }

    @Override
    public String getType() { return "FOUND"; }

    @Override
    public String toString() {
        return super.toString() + " | Finder: " + finder.getName();
    }
}

FILE: VoiceCommand.java


import java.io.BufferedReader;
import java.io.InputStreamReader;

public class VoiceCommand {
    public static String listen() {
        try {
            String script =
                    "Add-Type -AssemblyName System.Speech; " +
                    "$r = New-Object System.Speech.Recognition.SpeechRecognitionEngine; " +
                    "$r.SetInputToDefaultAudioDevice(); " +
                    "$r.LoadGrammar((New-Object System.Speech.Recognition.DictationGrammar)); " +
                    "$result = $r.Recognize([TimeSpan]::FromSeconds(8)); " +
                    "if ($result -ne $null) { $result.Text }";

            ProcessBuilder pb = new ProcessBuilder(
                    "powershell.exe", "-NoProfile", "-Command", script);
            pb.redirectErrorStream(true);

            Process process = pb.start();
            BufferedReader reader = new BufferedReader(
                    new InputStreamReader(process.getInputStream()));

            StringBuilder result = new StringBuilder();
            String line;
            while ((line = reader.readLine()) != null) {
                result.append(line).append(" ");
            }

            process.waitFor();
            return result.toString().trim();
        } catch (Exception e) {
            return "";
        }
    }
}

FILE: LostAndFoundGUI.java


import javax.swing.*;
import java.awt.*;
import java.util.ArrayList;

public class LostAndFoundGUI extends JFrame {
    private JTextField nameField, descriptionField, locationField, dateField;
    private JTextField studentIdField, studentNameField, courseField, searchField;
    private JComboBox<String> typeBox;
    private JTextArea outputArea;
    private ArrayList<Item> items = new ArrayList<>();
    private int nextId = 1;

    public LostAndFoundGUI() {
        setTitle("Student Lost and Found System");
        setSize(800, 650);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        createGUI();
    }

    private void createGUI() {
        JPanel mainPanel = new JPanel(new BorderLayout(10, 10));
        mainPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JLabel title = new JLabel("STUDENT LOST AND FOUND SYSTEM");
        title.setFont(new Font("Arial", Font.BOLD, 22));
        title.setHorizontalAlignment(SwingConstants.CENTER);
        mainPanel.add(title, BorderLayout.NORTH);

        JPanel formPanel = new JPanel(new GridLayout(9, 2, 5, 5));

        formPanel.add(new JLabel("Item Type:"));
        typeBox = new JComboBox<>(new String[]{"LOST", "FOUND"});
        formPanel.add(typeBox);

        formPanel.add(new JLabel("Item Name:"));
        nameField = new JTextField(); formPanel.add(nameField);

        formPanel.add(new JLabel("Description:"));
        descriptionField = new JTextField(); formPanel.add(descriptionField);

        formPanel.add(new JLabel("Location:"));
        locationField = new JTextField(); formPanel.add(locationField);

        formPanel.add(new JLabel("Date:"));
        dateField = new JTextField(); formPanel.add(dateField);

        formPanel.add(new JLabel("Student ID:"));
        studentIdField = new JTextField(); formPanel.add(studentIdField);

        formPanel.add(new JLabel("Student Name:"));
        studentNameField = new JTextField(); formPanel.add(studentNameField);

        formPanel.add(new JLabel("Course:"));
        courseField = new JTextField(); formPanel.add(courseField);

        formPanel.add(new JLabel("Search:"));
        searchField = new JTextField(); formPanel.add(searchField);

        mainPanel.add(formPanel, BorderLayout.WEST);

        outputArea = new JTextArea();
        outputArea.setEditable(false);
        outputArea.setFont(new Font("Monospaced", Font.PLAIN, 13));
        mainPanel.add(new JScrollPane(outputArea), BorderLayout.CENTER);

        JPanel buttonPanel = new JPanel(new GridLayout(2, 4, 5, 5));
        JButton addButton = new JButton("Add Item");
        JButton displayButton = new JButton("Display All");
        JButton searchButton = new JButton("Search");
        JButton clearButton = new JButton("Clear");
        JButton voiceButton = new JButton("Voice Command");
        JButton removeButton = new JButton("Remove Item");
        JButton exitButton = new JButton("Exit");

        buttonPanel.add(addButton);
        buttonPanel.add(displayButton);
        buttonPanel.add(searchButton);
        buttonPanel.add(clearButton);
        buttonPanel.add(voiceButton);
        buttonPanel.add(removeButton);
        buttonPanel.add(exitButton);
        mainPanel.add(buttonPanel, BorderLayout.SOUTH);

        addButton.addActionListener(e -> addItem());
        displayButton.addActionListener(e -> displayItems());
        searchButton.addActionListener(e -> searchItems());
        clearButton.addActionListener(e -> clearFields());
        removeButton.addActionListener(e -> removeItem());
        exitButton.addActionListener(e -> System.exit(0));
        voiceButton.addActionListener(e -> startVoiceCommand());

        add(mainPanel);
    }

    private void addItem() {
        String name = nameField.getText().trim();
        String description = descriptionField.getText().trim();
        String location = locationField.getText().trim();
        String date = dateField.getText().trim();
        String studentId = studentIdField.getText().trim();
        String studentName = studentNameField.getText().trim();
        String course = courseField.getText().trim();

        if (name.isEmpty() || description.isEmpty() || location.isEmpty() ||
            date.isEmpty() || studentId.isEmpty() || studentName.isEmpty() || course.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Please fill in all fields.",
                    "Missing Information", JOptionPane.WARNING_MESSAGE);
            return;
        }

        Student student = new Student(studentId, studentName, course);
        Item item;

        if (typeBox.getSelectedItem().equals("LOST")) {
            item = new LostItem(nextId, name, description, location, date, student);
        } else {
            item = new FoundItem(nextId, name, description, location, date, student);
        }

        items.add(item);
        nextId++;
        outputArea.append("Item added successfully!\n" + item + "\n\n");
        clearFields();
    }

    private void displayItems() {
        outputArea.setText("");
        if (items.isEmpty()) {
            outputArea.setText("No items found.\n");
            return;
        }
        for (Item item : items) outputArea.append(item + "\n\n");
    }

    private void searchItems() {
        String keyword = searchField.getText().trim().toLowerCase();
        if (keyword.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Enter something to search.",
                    "Search", JOptionPane.WARNING_MESSAGE);
            return;
        }

        outputArea.setText("");
        boolean found = false;

        for (Item item : items) {
            if (item.getName().toLowerCase().contains(keyword)
                    || item.getDescription().toLowerCase().contains(keyword)
                    || item.getLocation().toLowerCase().contains(keyword)
                    || item.getType().toLowerCase().contains(keyword)) {
                outputArea.append(item + "\n\n");
                found = true;
            }
        }

        if (!found) outputArea.setText("No matching item found.\n");
    }

    private void removeItem() {
        String input = JOptionPane.showInputDialog(this, "Enter Item ID to remove:");
        if (input == null) return;

        try {
            int id = Integer.parseInt(input);
            boolean removed = false;

            for (int i = 0; i < items.size(); i++) {
                if (items.get(i).getId() == id) {
                    items.remove(i);
                    removed = true;
                    JOptionPane.showMessageDialog(this, "Item removed successfully.");
                    displayItems();
                    break;
                }
            }

            if (!removed) JOptionPane.showMessageDialog(this, "Item ID not found.");
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(this, "Please enter a valid number.");
        }
    }

    private void clearFields() {
        nameField.setText("");
        descriptionField.setText("");
        locationField.setText("");
        dateField.setText("");
        studentIdField.setText("");
        studentNameField.setText("");
        courseField.setText("");
        searchField.setText("");
    }

    private void startVoiceCommand() {
        outputArea.append("Listening... Please speak a command.\n");

        new Thread(() -> {
            String command = VoiceCommand.listen();

            SwingUtilities.invokeLater(() -> {
                if (command == null || command.isEmpty()) {
                    outputArea.append("No voice command detected.\n\n");
                    return;
                }

                outputArea.append("Voice command: " + command + "\n\n");
                processVoiceCommand(command);
            });
        }).start();
    }

    private void processVoiceCommand(String command) {
        String text = command.toLowerCase();

        if (text.contains("display") || text.contains("show") || text.contains("all items")) {
            displayItems();
        } else if (text.contains("search")) {
            String keyword = text.replace("search", "").trim();
            searchField.setText(keyword);
            searchItems();
        } else if (text.contains("clear")) {
            clearFields();
            outputArea.append("Fields cleared.\n\n");
        } else if (text.contains("exit") || text.contains("close")) {
            System.exit(0);
        } else {
            outputArea.append("Command not recognized.\n"
                    + "Try: display items, search phone, clear, or exit.\n\n");
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new LostAndFoundGUI().setVisible(true));
    }
}
