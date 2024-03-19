## Questions
# 1.	Read a text as secret and covert to binary then embedded , Extract the embedded text from the image
### Embedding 
```
original =imread('flower.jpg');
cover=rgb2gray(original);
[row,column]=size(cover);
L=256;
stego=cover;

message=input('Enter the message to be hidden: ','s');
len=strlength(message)*8;  %Each character will take 8 bits so total number of bits in the message will be len
ascii=uint8(message);   %ascii is a vector having the ascii value of each character
binary_separate=dec2bin(ascii,8); 
display(binary_separate);
%binary_separate is an array having the decimal representation of each ascii value
binary_all='';  %binary_all will have the entire sequence of bits of the message
for i=1:strlength(message)
    binary_all=append(binary_all,binary_separate(i,:));
end
display(binary_all);
count=1;    %initializing count with 1

for i=1:row
    for j=1:column
        
  %for every character in the message
        if count<=len
            %Obtain the LSB of the grey level of the pixel
            LSB=mod(cover(i,j),2);
            %Convert the bit from the message to numeric form
            a=str2double(binary_all(count));
%Perform XOR operation between the bit and the LSB
            temp=double(xor(LSB,a));
            
  %Change the bit of the stego image accordingly
            stego(i,j)=cover(i,j)+temp;
                    count=count+1;
        end
    end
end

subplot(1,2,1);
imshow(cover);
title('Cover Image');

subplot(1,2,2);
imshow(stego);
title('Stego Image');
```

### Extraction
```
count=1;
message_in_bits='';
for i=1:row
    for j=1:column
        %For all the characters in the message
        if count<=len

            %Retrieve the LSB of the intensity level of the pixel
            LSB=mod(stego(i,j),2);

            %Append into message_in_bits to get bit sequence of message
            message_in_bits=append(message_in_bits,num2str(LSB));

            count=count+1;
        end
    end
end

%Converting the bit sequence into the original message
i=1;
original_message='';
while i<=len
    %Take a set of 8 bits at a time
    %Convert the set of bits to a decimal number
    %Convert the decimal number which is the ascii value to its corresponding character
    %Append the obtained character into the resultant string
    original_message=append(original_message,char(bin2dec(message_in_bits(1,i:i+7))));
    i=i+8;
end
disp(['The original message is: ',original_message]);
```
# 2.	Calculate the psnr for the stego image, do visual attack and histogram attack
## PSNR
```
% Calculate PSNR for the stego image
psnr_value = psnr(stego, cover);
disp(['PSNR for the stego image: ', num2str(psnr_value), ' dB']);

```
### Visual Attack
```
% Visual Attack: Display the cover image and the stego image side by side for visual inspection
figure;
subplot(1,2,1);
imshow(cover);
title('Cover Image');
subplot(1,2,2);
imshow(stego);
title('Stego Image');
```
### Histogram Attack
```
% Histogram Attack: Display the histograms of cover and stego images for comparison
figure;
subplot(2,1,1);
imhist(cover);
title('Histogram of Cover Image');
subplot(2,1,2);
imhist(stego);
title('Histogram of Stego Image');
```
### Bit plane
```
%bit plane

C=imread('flower.jpg');
I=rgb2gray(C);
figure(1), imshow(I);
figure(2), subplot (2,4,1)
b1 = bitget(I,1);
imshow(b1,[]);
b2 = bitget(I,2);
subplot (2,4,2)
imshow(b2,[]);
b3 = bitget(I,3);
subplot (2,4,3)
imshow(b3,[]);
b4 = bitget(I,4);
subplot (2,4,4)
imshow(b4,[]);
b5 = bitget(I,5);
subplot (2,4,5)
imshow(b5,[]);
b6 = bitget(I,6);
subplot (2,4,6)
imshow(b6,[]);
b7 = bitget(I,7);
subplot (2,4,7)
imshow(b7,[]);
b8 = bitget(I,8);
subplot (2,4,8)
imshow(b8,[]);
```
# 3.	https://wiki.bi0s.in/steganography/jsteg/
## Installation
```
$ sudo wget -O /usr/bin/jsteg https://github.com/lukechampine/jsteg/releases/download/v0.1.0/jsteg-linux-amd64
$ sudo chmod +x /usr/bin/jsteg
$ sudo wget -O /usr/bin/slink https://github.com/lukechampine/jsteg/releases/download/v0.2.0/slink-linux-amd64
$ chmod +x /usr/bin/slink
```
![image](https://github.com/udayk01/Steganography/assets/52235763/cde4f7d5-8971-42a3-bb5d-21cfd4ab2a26)

> Jsteg is successfully installed

> Download a image file of any format and store in your directory, here i am using a file image.jpg and create a simple text file which contains the message to be embeded in the file

![image](https://github.com/udayk01/Steganography/assets/52235763/b1b7f042-17fd-45a7-b242-02634db7c025)

## Hiding the data

```
jsteg hide <in.jpg> <secret file name> <out.jpg>
```

> Where in.jpg is our original file, secret file name is the name of text file and out.jpg is name of modified image file

![image](https://github.com/udayk01/Steganography/assets/52235763/5c80e32a-52cd-4f48-b423-28184c956d5b)

> Now the message is embedded into the new jpg file called hidden.jpg 

## Revealing the data

![image](https://github.com/udayk01/Steganography/assets/52235763/b73b0a33-3b70-4379-a548-43332d2f327f)

> Message revealed successfully

# 4.	Check the maximum payload jsteg can embed

![image](https://github.com/udayk01/Steganography/assets/52235763/1d125fea-49c4-4d8b-9979-2093fceaff67)

> 1920*1200 = 2304000

> 2304000 / 8 = 288000

> It is the maximum payload jsteg can embed.

# 5.	Calculate the psnr for the stego image, do visual attack and histogram attack after jsteg
## PSNR 
```
import sys
from PIL import Image
import numpy as np

if len(sys.argv) != 3:
    print(f"usage: python3 {sys.argv[0]} <cover image> <stego image>")
    sys.exit(1)

# load the cover and stego images
cover_img = Image.open(sys.argv[1])
stego_img = Image.open(sys.argv[2])

# convert the images to numpy arrays
cover_npy = np.array(cover_img)
stego_npy = np.array(stego_img)

# calculate mse
mse = np.mean((cover_npy - stego_npy) ** 2)
MAX_PKL_VAL = 255.0

# calculate psnr
psnr = 10 * np.log10((MAX_PKL_VAL ** 2) / mse)
print(f"Peak Signal to Noise Ratio: {psnr}")
```

![image](https://github.com/udayk01/Steganography/assets/52235763/16003406-4b37-4983-850e-9c2110123d36)

### Histogram 
```
import sys
import matplotlib.pyplot as plt
from PIL import Image

if len(sys.argv) != 2:
    print(f"usage: python3 {sys.argv[0]} <image file path>")
    sys.exit(1)

# Path to the image file.
image_path = sys.argv[1]

# Open the image using Pillow
image = Image.open(image_path).convert('L')  # Convert to grayscale

# Get histogram data as a list
histogram = image.histogram()

# Create a dictionary from the histogram list
hist_dict = {}
for intensity, count in enumerate(histogram):
    hist_dict[intensity] = count

# Generate the histogram plot using Matplotlib
plt.figure()
plt.bar(hist_dict.keys(), hist_dict.values())
plt.xlabel("Pixel Intensity")
plt.ylabel("Pixel Count")
plt.title("Histogram of " + image_path)

# Access histogram data (e.g., print intensity with highest pixel count)
max_intensity = max(hist_dict, key=hist_dict.get)
print(f"Intensity with highest pixel count: {max_intensity} (count: {hist_dict[max_intensity]})")

# Show the histogram plot
plt.show()
```

![image](https://github.com/udayk01/Steganography/assets/52235763/64ca7d1a-0cff-4010-bffd-1a4b5951a298)

![image](https://github.com/udayk01/Steganography/assets/52235763/a30f44d0-f3fb-4765-a539-a787549bbc79)

