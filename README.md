first pico 
import pandas as pd
import hashlib
from Crypto.Util import number
import timeit

# Load the dataset
dataset_path = '/Users/kishore/Desktop/mutualfunds.csv'
df = pd.read_csv(dataset_path)

# MD5 Encryption
def md5_encrypt(data):
    md5 = hashlib.md5()
    md5.update(data.encode('utf-8'))
    return md5.hexdigest()

# El Gamal Encryption
def elgamal_encrypt(message, p, g, public_key):
    k = number.getRandomRange(2, p-1)
    c1 = pow(g, k, p)
    s = pow(public_key, k, p)
    c2 = (s * message) % p
    return c1, c2

# Encryption
p = 23  # Replace with a large prime number
g = 5   # Replace with a primitive root modulo p
public_key = 7  # Replace with a public key

# MD5 Encryption
df['encrypted_scheme_name_md5'] = df['scheme_name'].apply(md5_encrypt)
print("MD5 Encrypted Messages:")
print(df['encrypted_scheme_name_md5'])
print("\n")

# El Gamal Encryption
df['encrypted_scheme_name_elgamal'] = df['scheme_name'].apply(lambda x: elgamal_encrypt(int.from_bytes(x.encode(), 'big'), p, g, public_key))
print("El Gamal Encrypted Messages:")
print(df['encrypted_scheme_name_elgamal'])
print("\n")

# Time Complexity Measurement
start_time_md5 = timeit.default_timer()
df['encrypted_scheme_name_md5'] = df['scheme_name'].apply(md5_encrypt)
elapsed_time_md5 = timeit.default_timer() - start_time_md5

start_time_elgamal = timeit.default_timer()
df['encrypted_scheme_name_elgamal'] = df['scheme_name'].apply(lambda x: elgamal_encrypt(int.from_bytes(x.encode(), 'big'), p, g, public_key))
elapsed_time_elgamal = timeit.default_timer() - start_time_elgamal

print(f"MD5 Encryption Time: {elapsed_time_md5:.6f} seconds")
print(f"El Gamal Encryption Time: {elapsed_time_elgamal:.6f} seconds")
print("\n")

# Split the dataset into training and testing sets
train_data = df.sample(frac=0.8, random_state=1)
test_data = df.drop(train_data.index)

# Accuracy calculation
def calculate_accuracy(true_values, encrypted_values):
    return (true_values == encrypted_values).mean()

accuracy_md5 = calculate_accuracy(test_data['scheme_name'].apply(md5_encrypt), test_data['encrypted_scheme_name_md5'])
accuracy_elgamal = calculate_accuracy(test_data['scheme_name'].apply(lambda x: elgamal_encrypt(int.from_bytes(x.encode(), 'big'), p, g, public_key)), test_data['encrypted_scheme_name_elgamal'])

print(f'MD5 Accuracy: {accuracy_md5}')
print(f'El Gamal Accuracy: {accuracy_elgamal}')
if(elapsed_time_elgamal>elapsed_time_md5 ):
    print("MD5 TAKES LESS TIME THANK EL GAMAL ")
else:
    print("EL GAMAL TAKES LESS TIME THANK MD5")
