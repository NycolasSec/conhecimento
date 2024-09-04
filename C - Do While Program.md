```c
#include <stdio.h>

int main(void)
{
    int number;
    int max = -100000;
    int counter = 0;

    do {
        scanf("%d",&number);
        if(number != -1) 
            counter++;
            if(number > max)
                max = number;
    }
	while (number != -1);
		if(counter)
          printf("The largest number is %d \n",max);
		else 
			printf("Hey, you haven't entered any number!");
    return 0;
}
```