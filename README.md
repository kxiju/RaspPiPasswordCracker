# RaspPiPasswordCracker
!DISCLAIMER! This is used for personal security NOT any malicious activites.

 - Open IDLE, create a new file, and save it as encrypt.py.
 - First, import the EnigmaMachine class from Py-enigma by adding this code to your file:
    - from enigma.machine import EnigmaMachine
  
    - n your Python file, set up an EnigmaMachine object using the settings from your settings sheet. Each setting should be a string and should be typed exactly as it appears on the settings sheet. For example, the rotors will be set as 'IV I V'.
# Set up the Enigma machine
machine = EnigmaMachine.from_key_sheet(
   rotors='',
   reflector='B',
   ring_settings='',
   plugboard_settings='')
As we said, we’ll be using reflector B for all of our programs.

Write another line of code to set the rotor start positions to the settings from the sheet.
# Set the initial position of the Enigma rotors
machine.set_display('FNZ')
Choose three random letters to use as your message key — we will use “BFR”, but you can choose whatever you like. Encrypt the message key and make a note of the result. This is the encrypted key you will send with your message.
# Encrypt the text 'BFR' and store it as msg_key
msg_key = machine.process_text('BFR')
print(msg_key)
Write a line of code to reset the rotor start positions to your unencrypted message key (in our example, “BFR”).

Write some code to process the plaintext “RASPBERRYPI” and display the resulting ciphertext.

If you used the message key “BFR”, the resulting ciphertext should be “GON XXLXYFQNZIK”. If you’ve chosen a different message key, your result will be different.

You can also run pyenigma from the command line if you wish. If you type this command into a terminal window, it will produce the same result as the script you just wrote:

pyenigma.py -r IV I V -i 20 5 10 -p SX KU QP VN JG TC LA WM OB ZF -u B --start BFR --text "RASPBERRYPI"

Inside your file, import the EnigmaMachine class, and set up the machine with the settings shown on the settings sheet. Like last time, use reflector B.

Add some code to set the initial positions of the rotors to U, Y, and T to match the sending machine.

The other operator sent you “PWE” as the key for this message. Before sending, the key was encrypted to prevent an eavesdropper from being able to read it.

You first need to use your Enigma machine to recover the actual message key by decrypting “PWE” using the settings sheet’s rotor start positions: U, Y, and T.

Add the following code to decrypt the key, and run your program to display result:
# Decrypt the text 'PWE' and store it as msg_key
msg_key = machine.process_text('PWE')
print(msg_key)
Add some code at the bottom of the program to set the Enigma machine’s rotor starting positions to the decrypted message key you just obtained.

machine.set_display(msg_key)
ciphertext = 'YJPYITREDSYUPIU'
plaintext = machine.process_text(ciphertext)
print(plaintext)

you should see the script exiting without any errors, and the decrypted message “THISXISXWORKING”.



We will use this cribtext to help us launch the brute-force attack:

ciphertext = "YJPYITREDSYUPIU"
cribtext = "THISXISXWORKING"
We will know that the attack has found the correct rotor choices and starting positions when the cipher text is decrypted to the crib text.

Create a new Python file and save it as bruteforce_standalone.py.

Add the variables containing the cipher text and the crib text as strings.

We need to represent the selection of three out of five rotor wheels in our Python code. We could write code to generate the possibilities, but as there aren’t very many, we can manually define them.

Copy this list of all possible rotor choices into your file:
rotors = [ "I II III", "I II IV", "I II V", "I III II",
"I III IV", "I III V", "I IV II", "I IV III",
"I IV V", "I V II", "I V III", "I V IV",
"II I III", "II I IV", "II I V", "II III I",
"II III IV", "II III V", "II IV I", "II IV III",
"II IV V", "II V I", "II V III", "II V IV",
"III I II", "III I IV", "III I V", "III II I",
"III II IV", "III II V", "III IV I", "III IV II",
"III IV V", "IV I II", "IV I III", "IV I V",
"IV II I", "IV II III", "IV I V", "IV II I",
"IV II III", "IV II V", "IV III I", "IV III II",
"IV III V", "IV V I", "IV V II", "IV V III",
"V I II", "V I III", "V I IV", "V II I",
"V II III", "V II IV", "V III I", "V III II",
"V III IV", "V IV I", "V IV II", "V IV III" ]
Our strategy will be to select each set of rotor choices in the rotors list in turn and check to see whether decrypting the cipher text with this combination of rotors obtains the crib text.

However, it is not as simple as just testing every single possible choice of rotors. Inside our function, we will also need to search through all possible rotor start positions for each rotor combination.

For the time being, we will assume the slip ring settings “1 1 1” and the plugboard settings “AV BS CG DL FU HZ IN KM OW RX” — we’ll discuss adding these later. The code breakers at Bletchley Park would not have had this luxury!

Create a function called find_rotor_start() which takes three arguments: the rotor choice, the cipher text, and the crib text.


Inside your function, add the code to import the EnigmaMachine class:
from enigma.machine import EnigmaMachine
We have imported the Py-Enigma module inside our function for a reason: this allows us to reuse this code later on with an OctaPi cluster, on which we can run the script massively in parallel, and thus in much shorter time than on a single processor.

Write code inside the function to test all possible rotor start positions for the given rotor choice. Remember that we are passing a rotor choice into the function, so you only need to test all start positions for the specified rotor choice, not for every possible rotor choice!

def find_rotor_start( rotor_choice, ciphertext, cribtext ):
from enigma.machine import EnigmaMachine
alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

machine = EnigmaMachine.from_key_sheet(
rotors=rotor_choice,

B',

ring_settings='1 1 1°,

AV BS CG DL FU HZ IN KM OW RX')

reflector

plugboard_setting

# Do a search over all possible rotor starting positions
for rotorl in alphabet: # Search for rotor 1 start position
for rotor2 in alphabet: # Search for rotor 2 start position

for rotor3 in alphabet: # Search for rotor 3 start position

# Generate a possible rotor start position
start_pos = rotorl + rotor2 + rotor3

# Set the starting position
machine. set_display(start_pos)

# Attempt to decrypt the plaintext
plaintext = machine.process_text (ciphertext)
print( plaintext )

# Check if decrypted version is the same as the crib text
if plaintext == cribtext:
print("Valid settings found!")

return rotor_choice, start_pos

# If we didn't manage to successfully decrypt the message

return rotor_choice, "Cannot find settings”

for rotor_setting in rotors:
rotor_choice, start_pos = find_rotor_start( rotor_setting, ciphertext, cribtext )
print (rotor_choice +

+ start_pos )

if start_pos
break

“Cannot find settings”:

Save and run your program. It will take quite a long time to run, but as it executes, you should see the results for each rotor choice.

Once your brute-force attack has found the rotors and start position, plug them into the decrypt.py program you wrote earlier and decrypt the full secret message!

The settings were as follows: rotors = II V III / starting position = SCC
The secret message reads "THISXISXWORKINGXOCTAPIXISXAWESOME"
