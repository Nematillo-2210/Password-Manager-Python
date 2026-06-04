import cryptography
from cryptography.fernet import Fernet
import hashlib
import base64
import json as js
import os
def hash_password(message):
  
  hash_hex = hashlib.sha256(message.encode()).hexdigest()
  return hash_hex
  
def verify_password(text, hash):
  if hash_password(text) == hash:
    return True
  else: return False

def generate_key(master_password):
 master_password = base64.urlsafe_b64encode(hashlib.sha256(master_password.encode()).digest())
 return master_password
  

class PasswordManager():
  def __init__(self):
    self.passwords = []

  def add(self, master_password):
    website = input('Type in your website: ')
    name = input('Type in your name: ')
    password = input('Type in your password: ')
    key = generate_key(master_password)
    f = Fernet(key)
    password = f.encrypt(password.encode()) 
    to_dict = {
      'website': website, 'name': name, 'password': password
    }
    self.passwords.append(to_dict)
    
  def view(self, master_password):
    key = generate_key(master_password)
    f = Fernet(key)
    for entry in self.passwords:
      decrypted_password = f.decrypt(entry['password'])
      print(entry['website'], entry['name'], decrypted_password.decode())

    
  def delete(self):
    website = input("Type in the website you'd like to delete: ")
    found = False
    for i in self.passwords:
      if website == i['website']:
        self.passwords.remove(i)
        found = True
    if not found:
      print('Website Not Found')

def setup_master_password():
  master_psw = input('Please create a master password: ')
  hashed_master = hash_password(master_psw)
  with open('master.txt', 'w') as f:
    f.write(hashed_master)
 
def verify_master_password():
  verify_psw = input('Please provide the master password: ')
  with open('master.txt', 'r') as f:
    stored_hash = f.read()
  if verify_password(verify_psw, stored_hash):
    return verify_psw
  else: 
    return None
  

def save_passwords(password_manager):
  data = []
  for p in password_manager.passwords:
    data.append({'website': p['website'], 'name': p['name'], 'password': p['password'].decode()})
  with open('passwords.json', 'w') as f:
      js.dump(data, f)


def load_passwords():
  password_manager = PasswordManager()
  with open('passwords.json', 'r') as f:
    content = js.load(f)
    for p in content:
     password_manager.passwords.append(({'website': p['website'], 'name': p['name'], 'password': p['password'].encode()}))
    return password_manager 
  

while True:
  if not os.path.exists("master.txt"):
    setup_master_password()
    break
  else:
    master_password = verify_master_password()
    if master_password:
      break
    else:
      print('Wrong password')

try:
  manager = load_passwords()
except FileNotFoundError:
  manager = PasswordManager()

while True:
  try:
    menu = int(input(""" 
    Menu:
    1: Add
    2: View
    3: Delete
    4: Quit
    """))
    if menu == 1:
      manager.add(master_password)
      save_passwords(manager)

    elif menu == 2:
      manager.view(master_password)
  

    elif menu == 3:
      manager.delete()
      save_passwords(manager)
    elif menu <1 or menu >4:
      print("You can only choose from 1 to 4")

    elif menu == 4:
      break
  except Exception as e : print(e)
