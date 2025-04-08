# exam1sup
class DayNode {
    String date;
    List<String> students;
    DayNode next;

    public DayNode(String date, List<String> students) {
        this.date = date;
        this.students = students;
        this.next = null;
    }
}
class StudentNode {
    String name;
    int attendedClasses;
    List<String> explanations;
    StudentNode next;

    public StudentNode(String name) {
        this.name = name;
        this.attendedClasses = 0;
        this.explanations = new ArrayList<String>();
        this.next = null;
    }
}
class AttendanceProcessor {
    public static DayNode createAttendanceList(String filePath) throws IOException {
        DayNode head = null, tail = null;

        BufferedReader reader = new BufferedReader(new FileReader(filePath));
        String line;

        while ((line = reader.readLine()) != null) {
            String[] parts = line.split(",");
            String date = parts[0];
            List<String> students = new ArrayList<String>(Arrays.asList(Arrays.copyOfRange(parts, 1, parts.length)));

            DayNode newNode = new DayNode(date, students);

            if (head == null) {
                head = newNode;
                tail = head;
            } else {
                tail.next = newNode;
                tail = newNode;
            }
        }
        reader.close();
        return head;
    }
    public static StudentNode createStudentList(DayNode attendanceHead) {
        StudentNode studentHead = null, studentTail = null;

        DayNode currentDay = attendanceHead;
        while (currentDay != null) {
            for (String studentName : currentDay.students) {
                StudentNode studentNode = studentHead;
                boolean found = false;

                while (studentNode != null) {
                    if (studentNode.name.equals(studentName)) {
                        studentNode.attendedClasses++;
                        studentNode.explanations.add(currentDay.date);
                        found = true;
                        break;
                    }
                    studentNode = studentNode.next;
                }

                if (!found) {
                    StudentNode newStudent = new StudentNode(studentName);
                    newStudent.attendedClasses = 1;
                    newStudent.explanations.add(currentDay.date);

                    if (studentHead == null) {
                        studentHead = newStudent;
                        studentTail = studentHead;
                    } else {
                        studentTail.next = newStudent;
                        studentTail = newStudent;
                    }
                }
            }
            currentDay = currentDay.next;
        }
        return studentHead;
    }
 public static void updateStudentList(String filePath, StudentNode studentHead) throws IOException {
        BufferedReader reader = new BufferedReader(new FileReader(filePath));
        String line;

        while ((line = reader.readLine()) != null) {
            String[] parts = line.split(",");
            if (parts.length < 3) {
                System.err.println("Invalid line format: " + line);
                continue;
            }
            String studentName = parts[0];
            String date = parts[1];
            String explanation = parts[2];

            StudentNode studentNode = studentHead;
            while (studentNode != null) {
                if (studentNode.name.equals(studentName)) {
                    studentNode.attendedClasses++;
                    studentNode.explanations.add(date + ": " + explanation);
                    break;
                }
                studentNode = studentNode.next;
            }
        }
        reader.close();
    }

    public static void writeStudentListToFile(String filePath, StudentNode studentHead) throws IOException {
        BufferedWriter writer = new BufferedWriter(new FileWriter(filePath));

        StudentNode studentNode = studentHead;
        while (studentNode != null) {
            String explanations = String.join("; ", studentNode.explanations);
            writer.write(studentNode.name + "," + studentNode.attendedClasses + "," + explanations + "\n");
            studentNode = studentNode.next;
        }
        writer.close();
    }
}

public class Main {
    public static void main(String[] args) {
        try {
            String attendanceFile = "attendance.csv";
            String updatesFile = "updates.csv";
            String outputFile = "student_summary.csv";

            DayNode attendanceHead = AttendanceProcessor.createAttendanceList(attendanceFile);
            StudentNode studentHead = AttendanceProcessor.createStudentList(attendanceHead);
            AttendanceProcessor.updateStudentList(updatesFile, studentHead);
            AttendanceProcessor.writeStudentListToFile(outputFile, studentHead);

            System.out.println("Student data processed successfully!");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
