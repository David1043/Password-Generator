# Password-Generator
 In response to rising cyber threats, using different complex passwords for various accounts is crucial. Password generators are helpful for creating strong passwords and to enhance security.
import java.util.Scanner;

public class Alphabet {
    public static final String UPPERCASE_LETTERS = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    public static final String LOWERCASE_LETTERS = "abcdefghijklmnopqrstuvwxyz";
    public static final String NUMBERS = "1234567890";
    public static final String SYMBOLS = "!@#$%^&*()-_=+\\/~?";

    private final StringBuilder pool;

    public Alphabet(boolean uppercaseIncluded, boolean lowercaseIncluded, boolean numbersIncluded, boolean specialCharactersIncluded) {
        pool = new StringBuilder();
        
        if (uppercaseIncluded) pool.append(UPPERCASE_LETTERS);
        if (lowercaseIncluded) pool.append(LOWERCASE_LETTERS);
        if (numbersIncluded) pool.append(NUMBERS);
        if (specialCharactersIncluded) pool.append(SYMBOLS);
    }

    public String getAlphabet() {
        return pool.toString();
    }
}

class Password {
    private final String value;

    public Password(String s) {
        this.value = s;
    }

    public int calculateScore() {
        int score = 0;

        // Check if password contains uppercase letters
        boolean containsUpperCase = false;
        for (int i = 0; i < value.length(); i++) {
            if (Character.isUpperCase(value.charAt(i))) {
                containsUpperCase = true;
                break;
            }
        }
        if (containsUpperCase) score++;

        // Check if password contains lowercase letters
        boolean containsLowerCase = false;
        for (int i = 0; i < value.length(); i++) {
            if (Character.isLowerCase(value.charAt(i))) {
                containsLowerCase = true;
                break;
            }
        }
        if (containsLowerCase) score++;

        // Check if password contains digits
        boolean containsDigit = false;
        for (int i = 0; i < value.length(); i++) {
            if (Character.isDigit(value.charAt(i))) {
                containsDigit = true;
                break;
            }
        }
        if (containsDigit) score++;

        // Check if password contains symbols
        boolean containsSymbol = false;
        String symbols = "!@#$%^&*()-_=+\\/~?";
        for (int i = 0; i < value.length(); i++) {
            if (symbols.contains(String.valueOf(value.charAt(i)))) {
                containsSymbol = true;
                break;
            }
        }
        if (containsSymbol) score++;

        // Check length
        if (value.length() >= 8) score++;
        if (value.length() >= 16) score++;

        return score;
    }

    @Override
    public String toString() {
        return value;
    }
}

public class Generator {
    private final Alphabet alphabet;
    private static Scanner keyboard;

    public Generator(boolean IncludeUpper, boolean IncludeLower, boolean IncludeNum, boolean IncludeSym) {
        alphabet = new Alphabet(IncludeUpper, IncludeLower, IncludeNum, IncludeSym);
    }

    public void mainLoop() {
        keyboard = new Scanner(System.in);
        System.out.println("Welcome to Password Services :)");
        printMenu();

        String userOption = "-1";

        while (!userOption.equals("4")) {
            userOption = keyboard.next();

            switch (userOption) {
                case "1" -> {
                    requestPassword();
                    printMenu();
                }
                case "2" -> {
                    checkPassword();
                    printMenu();
                }
                case "3" -> {
                    printUsefulInfo();
                    printMenu();
                }
                case "4" -> printQuitMessage();
                default -> {
                    System.out.println("Kindly select one of the available commands");
                    printMenu();
                }
            }
        }
        keyboard.close();
    }

    private Password generatePassword(int length) {
        StringBuilder pass = new StringBuilder();

        int alphabetLength = alphabet.getAlphabet().length();
        int max = alphabetLength - 1;
        int min = 0;
        int range = max - min + 1;

        for (int i = 0; i < length; i++) {
            int index = (int) (Math.random() * range) + min;
            pass.append(alphabet.getAlphabet().charAt(index));
        }

        return new Password(pass.toString());
    }

    private void printUsefulInfo() {
        System.out.println();
        System.out.println("Use a minimum password length of 8 or more characters if permitted");
        System.out.println("Include lowercase and uppercase alphabetic characters, numbers and symbols if permitted");
        System.out.println("Generate passwords randomly where feasible");
        System.out.println("Avoid using the same password twice (e.g., across multiple user accounts and/or software systems)");
        System.out.println("Avoid character repetition, keyboard patterns, dictionary words, letter or number sequences," +
                "\nusernames, relative or pet names, romantic links (current or past) " +
                "and biographical information (e.g., ID numbers, ancestors' names or dates).");
        System.out.println("Avoid using information that the user's colleagues and/or " +
                "acquaintances might know to be associated with the user");
        System.out.println("Do not use passwords which consist wholly of any simple combination of the aforementioned weak components");
    }

    private void requestPassword() {
        boolean IncludeUpper = false;
        boolean IncludeLower = false;
        boolean IncludeNum = false;
        boolean IncludeSym = false;

        boolean correctParams;

        System.out.println();
        System.out.println("Hello, welcome to the Password Generator :) Answer the following questions with Yes or No.\n");

        do {
            String input;
            correctParams = false;

            do {
                System.out.println("Do you want lowercase letters (abcd...) to be used?");
                input = keyboard.next();
                passwordRequestError(input);
            } while (!input.equalsIgnoreCase("yes") && !input.equalsIgnoreCase("no"));
            if (isInclude(input)) IncludeLower = true;

            do {
                System.out.println("Do you want uppercase letters (ABCD...) to be used?");
                input = keyboard.next();
                passwordRequestError(input);
            } while (!input.equalsIgnoreCase("yes") && !input.equalsIgnoreCase("no"));
            if (isInclude(input)) IncludeUpper = true;

            do {
                System.out.println("Do you want numbers (1234...) to be used?");
                input = keyboard.next();
                passwordRequestError(input);
            } while (!input.equalsIgnoreCase("yes") && !input.equalsIgnoreCase("no"));
            if (isInclude(input)) IncludeNum = true;

            do {
                System.out.println("Do you want symbols (!@#$...) to be used?");
                input = keyboard.next();
                passwordRequestError(input);
            } while (!input.equalsIgnoreCase("yes") && !input.equalsIgnoreCase("no"));
            if (isInclude(input)) IncludeSym = true;

            //No pool selected
            if (!IncludeUpper && !IncludeLower && !IncludeNum && !IncludeSym) {
                System.out.println("You have selected no characters to generate your " +
                        "password, at least one of your answers should be Yes\n");
                correctParams = true;
            }

        } while (correctParams);

        System.out.println("Great! Now enter the length of the password:");
        int length = keyboard.nextInt();

        final Generator generator = new Generator(IncludeUpper, IncludeLower, IncludeNum, IncludeSym);
        final Password password = generator.generatePassword(length);

        System.err.println("Your generated password -> " + password);
    }

    private boolean isInclude(String input) {
        if (input.equalsIgnoreCase("yes")) {
            return true;
        } else {
            return false;
        }
    }

    private void passwordRequestError(String input) {
        if (!input.equalsIgnoreCase("yes") && !input.equalsIgnoreCase("no")) {
            System.out.println("You have entered something incorrect. Let's go over it again.\n");
        }
    }

    private void checkPassword() {
        String input;

        System.out.print("\nEnter your password:");
        input = keyboard.next();

        final Password p = new Password(input);

        System.out.println(p.calculateScore());
    }

    private void printMenu() {
        System.out.println();
        System.out.println("Enter 1 - Password Generator");
        System.out.println("Enter 2 - Password Strength Check");
        System.out.println("Enter 3 - Useful Information");
        System.out.println("Enter 4 - Quit");
        System.out.print("Choice: ");
    }

    private void printQuitMessage() {
        System.out.println("Closing the program. Goodbye!");
    }

    public static void main(String[] args) {
        Generator generator = new Generator(true, true, true, true);
        generator.mainLoop();
    }
}

public class Main {
    public static final Scanner keyboard = new Scanner(System.in);

    public static void main(String[] args) {
        Generator generator = new Generator(true, true, true, true);
        generator.mainLoop();
    }
}
