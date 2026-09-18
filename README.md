# PROG5121_POEPT1
Part 1 submission
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 */

package com.mycompany.prog5121poept1;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Scanner;
/**
 *
 * @author emsla
 */
public class PROG5121POEPT1 {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        ArrayList<Login>users = new ArrayList<>(); //Store the list of users
        
        System.out.println("\n[System] Carrier Pigeons are on standby.");
        System.out.println("[System] User database is synced and initialized.");
        System.out.println("[System] Secure connections have been enabled.");
        System.out.println("[System] Bird feed has been divided.");
        
        System.out.println("======================================");
        System.out.println("        CARRIER PIGEON LOGIN          ");
        System.out.println("======================================");
        System.out.println("  Secure messages, delivered safely.  ");
        System.out.println("======================================");
       
        while(true){
            System.out.println("\nMain Menu:");
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.println("Choose an option:");   //display menu
           
            int choice = scanner.nextInt();
            scanner.nextLine();
           
            switch(choice){
                case 1: //Registry
                    System.out.print("Enter username:");
                    String username = scanner.nextLine();
                    System.out.println("Username successfully assigned!");
 
                    boolean validUsername = username.contains("_")&&username.length()<=9;
                    if(!validUsername){
                        System.out.println("Username must contain at least one underscore(_)and be no longer than 9 characters long.");
                        break; //confirmation of username format
                    }
                    System.out.print("Enter password:");
                    String password = scanner.nextLine();
                    System.out.println("Password requirements met. Password successfully captured!");
                    
                    boolean validPassword =password.length()>8 && password.matches(".*[A-Z].*") && password.matches(".*\\d.*")&&
                            password.matches(".*[!@#$%^&*(),.?\":{}|<>].*");
                    if(!validPassword){
                        System.out.println("Password must be at least 9 characters long, contain an uppercase letter, number and special characters");
                        break; //Confirmation of password format
                    }
                    System.out.print("Enter A SA phone number:(e.g. +278311111111):");
                    String phoneNumber = scanner.nextLine();
                    System.out.println("Cellphone number is successfully added!");
                    
                    String regex ="\\+27[6-8]\\d{8}";
                    if(!phoneNumber.matches(regex)){
                        System.out.println("Invalid phone number format! use +27xxxxxxxxx");
                        break; //confirmation of phone number format
                    }
                    
                    boolean userExists = users.stream().anyMatch(u ->u.getUsername().equals(username));
                    if(userExists){
                        System.out.println("Username already exists. Please choose another username.");
                        break;
                    }
                    
                    users.add(new Login(username, password, phoneNumber));
                    System.out.println("User registered successfully!");
                    System.out.println("Welcome "+ username + "! You may now make use of your winged messenger.");
                    System.out.println("Redirecting you to login page.......\n");  //add the new user to the user database
                   
                    
                    promptLogin(scanner, users); //Prompt user login
                    break;
                   
                case 2:
                    if(users.isEmpty()){
                        System.out.println("No users are registered yet. Please register first."); //login
                        break;
                    }
                    promptLogin(scanner,users);
                    break;
                   
                case 3:
                    System.out.println("Returning to the nest."); //Exit
                    scanner.close();
                    return;
                   
                default:
                    System.out.println("Invalid option. Please choose 1,2 or 3");
            }
        }
    }
    private static void promptLogin(Scanner scanner,ArrayList<Login>users){
     System.out.print("Please enter username:");
     String enteredUsername = scanner.nextLine();
     
     Login user = users.stream()
          .filter(u->u.getUsername()
          .equals(enteredUsername))
          .findFirst()
          .orElse(null); 
     
      if (user == null) {
        System.out.println("User not found.");
        return;
    }

    int attempts = 3;

    while (attempts > 0) {  
       System.out.print("Please enter password:");
       String enteredPassword = scanner.nextLine();
     
    if (user.loginUser(enteredPassword)) {
            System.out.println("Login successful!");
            System.out.println("Welcome back, " + enteredUsername + "!");
            return;
        }
    
    attempts--;
        System.out.println("Invalid password.");

        if (attempts > 0) {
            System.out.println("Attempts remaining: " + attempts);
        }
    }
    
    System.out.println("Too many failed attempts. Returning to main menu."); // find user details and attempt login, deny entry on 3 failed password entries
    }
}
class Login{
    public String username;
    public String passwordHash;
    public String phoneNumber;
    public byte[]salt;
   
    public Login(String username, String password, String phoneNumber){
        this.username =username;
        this.salt = generateSalt();
        this.passwordHash= hashPassword(password,salt);
        this.phoneNumber = phoneNumber;
    }
    public String getUsername(){
        return username;
    }
    public boolean loginUser(String enteredPassword){
        return this.passwordHash.equals(hashPassword(enteredPassword, salt));
    }
    public String returnLoginStatus(boolean status){
        return status?"Login successful! ":"Invalid username or password.";
    }
    //hash password with salt using SHA-256
    private String hashPassword(String password, byte[]salt){
        try{
            MessageDigest md = MessageDigest.getInstance("SHA-256");
            md.update(salt);//add salt to the hash
            byte[]hash=md.digest(password.getBytes(java.nio.charset.StandardCharsets.UTF_8));
            StringBuilder hexString= new StringBuilder();
            for(byte b: hash){
                hexString.append(String.format("%02x", b));
               
            }
            return hexString.toString();
        }catch(NoSuchAlgorithmException e){
            throw new RuntimeException("Error hashing password", e);
        }
       
    }
    //Generate a random salt for password hashing
    private byte[]generateSalt(){
        try{
            SecureRandom random = new SecureRandom();
            byte[]salt=new byte[16];
            random.nextBytes(salt);
            return salt;
           
        }catch(Exception e){
            throw new RuntimeException("Error generating salt",e);
        }
    }
}
