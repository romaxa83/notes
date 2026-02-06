#python #algoritm #recipes

==Пример реализации стека, на примере проверки в закрывающихся скобок (")", "}", "]") если они есть в исходной строке==
```python
a = int("Введите строку: ")  
stack = []  
flVerify = True  
  
for lt in a:  
    if lt in "({[":  
        stack.append(lt)  
    elif lt in ")]}":  
        if len(stack) == 0:  
            flVerify = False  
            break  
        last = stack.pop()  
        if last == "(" and lt == ')':  
            continue  
        if last == "[" and lt == ']':  
            continue  
        if last == "{" and lt == '}':  
            continue  
  
        flVerify = False  
        break  
if flVerify and len(stack) == 0: # стек по итогам проверки должен быть пустым  
    print("yes")  
else:  
    print("no")
```