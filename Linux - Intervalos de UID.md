O Red Hat Enterprise Linux usa determinados números de UID e intervalos de números para fins específicos.

- **UID 0**: a UID da conta de superusuário (`root`).
    
- **UID 1-200**: UIDs da conta do sistema que são atribuídas estaticamente aos processos do sistema.
    
- **UID 201-999**: UIDs que são atribuídas a processos do sistema que não possuem arquivos neste sistema. O software que exige uma UID sem privilégios é atribuído dinamicamente a uma UID a partir desse pool disponível.
    
- **UID 1000+** : o intervalo de UIDs a ser atribuído a usuários regulares e sem privilégios.