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
