import java.util.PriorityQueue;

/**
 * My solution for the password checker problem. Please see the attached .pdf file for my detailed explanation.
 * @author Aghenitei Bianca-Ioana
 */


public class MainClass {
    public static int checkPassword(String password){
        int length;
        int noChanges = 0;

        int noMissingChars = 0;
        int noExcessChars = 0;
        int noMissingTypes = 3;
        Boolean lowerCase = false, upperCase = false, digit = false;

        // this queue will keep track of existing substrings of repeating characters.
        // override compare so that substrings with a lower modulo 3 value have a higher priority to be changed.
        // we prioritize strings with a lower modulo 3 value because deleting from those strings will result in
        // substrings of repeating characters that can be solved with less changes

        PriorityQueue<Integer> repeatingCharsLengthsQueue =new PriorityQueue<>((l1, l2) -> {
            Integer mod1 = l1 % 3;
            Integer mod2 = l2 % 3;
            return Integer.compare(mod1, mod2);
        });


        //compute missing and excess chars
        if(password.length() < 6) {
            noMissingChars = 6 - password.length();
        }
        if(password.length() > 20){
            noExcessChars = password.length() - 20;
        }


        //iterate through the string to compute the priority queue
        //while at it, compute missing types of chars
        int i = 0;
        int noRepetitions = 1;

        while(i < password.length() - 1 ){
            if(password.charAt(i) == password.charAt(i+1)){
                noRepetitions++;
            }
            else{
                if(noRepetitions >= 3 ){
                    //add repeating chars string to queue
                    repeatingCharsLengthsQueue.add(noRepetitions);
                }
                noRepetitions = 1;
            }

            //check if missing category condition is met
            Character currentChar = password.charAt(i);

            if(!lowerCase && Character.isLowerCase(currentChar)){
                lowerCase = true;
                noMissingTypes--;
            }
            if(!upperCase && Character.isUpperCase(currentChar)){
                upperCase = true;
                noMissingTypes--;
            }
            if(!digit && Character.isDigit(currentChar)){
                digit = true;
                noMissingTypes--;
            }

            //continue with next char
            i++;
        }

        //check if the last substring consists of repeating chars
        if(noRepetitions >= 3 ){
            //add repeating chars string to queue
            repeatingCharsLengthsQueue.add(noRepetitions);
        }
        //check if the last char is of a desired category
        if(!lowerCase && Character.isLowerCase(password.charAt(password.length() - 1))){
            lowerCase = true;
            noMissingTypes--;
        }
        if(!upperCase && Character.isUpperCase(password.charAt(password.length() - 1))){
            upperCase = true;
            noMissingTypes--;
        }
        if(!digit && Character.isDigit(password.charAt(password.length() - 1))){
            digit = true;
            noMissingTypes--;
        }

        //start counting changes


        //if we have to delete chars, try to delete from substrings that have a higher priority.
        //if we don't have such substrings, delete any char so that we don't break a condition
        while(noExcessChars > 0){
            //take the substring with the highest priority and reduce its length by one
            if(!repeatingCharsLengthsQueue.isEmpty()){
                length = repeatingCharsLengthsQueue.poll();
                length--;
                if(length >= 3)
                    repeatingCharsLengthsQueue.add(length);
            }

            noExcessChars--;
            noChanges++;
            System.out.println("Delete a char");
        }

        //if we have to add more chars, add chars of missing categories in positions that break up repeating chars substrings:
        //best placement is once every 2 repeating chars, which reduces substring length by 3. Adding to any substring is just as good.
        // For simplicity, we're trying to add to the first substring from the priority queue. If the priority queue is empty, just add
        //an unused char at any place in the password

        while(noMissingChars > 0){
            //check if we can break up a substring of repeating chars
            if(!repeatingCharsLengthsQueue.isEmpty()){
                length = repeatingCharsLengthsQueue.poll();
                length -= 3;
                if(length >= 3)
                    repeatingCharsLengthsQueue.add(length);
            }

            noMissingChars--;

            //add missing char types if needed to reduce the number of changes
            if(noMissingTypes > 0){
                noMissingTypes--;
            }

            noChanges++;
            System.out.println("Add a char");
        }


        //from now on, we don't want to solve missing requirements by adding characters, as we might risk overpassing the password's length limit
        //luckily, the unchecked conditions can be met by replacing certain chars with others that don't exist in the password.



        //check if there are still substrings of repeating chars break them up by replacing every third char with an unused char.
        //try to add a char of a missing category if needed.
        while(!repeatingCharsLengthsQueue.isEmpty()){
            length = repeatingCharsLengthsQueue.poll();
            length -= 3;
            if(length >= 3)
                repeatingCharsLengthsQueue.add(length);
            noMissingTypes--;
            noChanges++;
            System.out.println("Replace a char, as there are still substrings of repeating chars");
        }


        //check if there are still missing types
        while(noMissingTypes > 0){
            noMissingTypes--;
            noChanges++;
            System.out.println("Replace a char, as there are still missing categories");
        }

        return noChanges;
    }



    public static void main(String[] args) {
        String password = "ABABABABABABABABABAB1";
        int noChanges = checkPassword(password);
        System.out.println("We need to make " + noChanges + " changes!");

    }
}
