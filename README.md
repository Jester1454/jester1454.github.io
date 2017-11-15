# Краткое описание параметров игры
Есть три типа объекта - Unit, Base, Tower, для каждой из сторон (для Unit и Tower - две, для Base + нейтральная) все параметры (SeaBattleConfig.JSON)
выделены в отдельный блок, который соответственно назван.
## Юнит

### Словесное описание

Юнит - боевая единица каждого игрока. Если находится вне базы - имеет атаку, передвижение, текущее состояние здоровье, если находится в базе - увеличивает счетчик наполнения базы на один, и увелчивает текущее здоровье базы на состояние здоровье юнита.
Юнит имеет два типа передвижения:
1. Обыченое - из базы в базу, по пути может перестреливаться с вражескими юнитами, но не изменяет таргет движения.
2. Атакующее - если таргет аказался вражеской базы, то кружим вокруг базы нанося урон, в зависимости от приоритета атаки.
Соответственно юнит имеет скорость передвижения, которая представлена в конфигурационном поле ниже параметром maxSpeed;

Атака юнита соотвествует следующей игровой логики. Раз в DamageRate (для примера раз в секунду), атакуем объект (атакуем - уменьшаем здоровье атакуемого объекта на параметр Damage), который находится в радиусе атаки (AttackRadius) в соответсвии приоритету атаки. Если в результате перемещения враг вышел из радиуса атаки/ умер, тогда атакуем друго объект, находящийся в радиусе (если такой существует). Если в радиус атаки внезапно залетел объект с более высоким приоритетом атаки, тогда юнит начинает атаковать его. Приоритет атаки определен следующим образом:
Вражеский юнит > вражеского MotherShip > вражеской базы.

Здоровье юнита - если здоровье юнита в результате атаки уменьшается и оказывается ниже нуля, юнит погибает, если он при этом был в базе - то в базе уменьшается счетчик текущего числа юнитов.

### Конфигурационный файл
Конфигурационный файл можно изменять прям в этом репозитории, и спустя непродолжительное время (порядка пары минут) изменения будут автоматически применены в игре, после перезапуска игровой сцены. 

```javascript
    "Unit":
    {
        "maxHP": 15.0, //- максимальное ХП
        "Damage": 5.0, //- дамаг наносимый одним выстрелом
        "DamageRate": 1.0, //- время перезарядки (в секундах)
        "AttackRadius": 3.0, //- радиус вокруг корабля, в котором корабль умеет атаковать
        "maxSpeed": 2.5 //- максимально развиваемая скорость
    }
```
## База

### Словесное описание
База - стратегическая единица каждого игрока. База является аккумулятором боевых единиц игрока (юнитов), она отвечает за их спавн, за их производство, и за назначение юнитам (которые находятся в текущей базе) цели для перемещения. 
База обладает следующим набором поведенческих свойств:
0. Здоровье - у базы есть свое здоровье (MaxHP), которое не зависит от того, сколько в базе находится юнитов. Когда юнит появляется в базе (как бы это не произошло, производством или юнит зашел в базу из другой базы), то текущее здоровье увеличивается на параметр здоровья юнита. 

1. Аккумулирует входящих дружественных юнитов.В базу может зайти сколько угодно юнитов, но если их число перевалило за отметку MaxUnit, то производится юниты в данной базе больше не будут. Когда юнит заходит на базу - текущее число юнитов прибавляется, и текущее здоровье базы увеличивается на здоровье вошедшего юнита. При старте игры у базы есть определенное число юнитов - StartCountUnit.
 
2. Производит юнитов. Каждая база (кроме нейтральной) может производить юнитов раз в определенный отрезок времени - ProduceUnitRate. Если юнитов в результате перемещения или производства в базе оказалось больше, чем MaxUnit, то производство юнитов приостанавливается и возобновляется лишь тогда, когда текущее количество юнитов в базе становится меньше, чем MaxUnit. Каждый раз, когда юнит был произведет, то текущее количество юнитов в базе повышается на единицу, так же и текущее здоровье базы прибавляется на здоровье юнита.

3. Спавнит юнитов - при помощи жеста, из одной базы в другую, юниты начинаются спавниться и двигаться в направлении выбранного игроком таргета. Количество заспавленных юнитов будет равно текущему (в момент начала спавна) количеству юнитов в базе (currentCountUnit). Спавн прекращается, когда в базе юнитов остается = 0 юнитов, либо тогда, когда предпалагаемое число уже заспавнилось. При спавне юнитов - текущее количество здоровья базы уменьшается на здоровье заспавленных юнитов. Соответственно уменьшается и текущее количество юнитов. (??? - сейчас и далее таким значком будут установлены либо недоработанные, либо несозданные механики) Сейчас скорость спавна постоянна, и ни как не меняется в зависимости от параметра, не известно как будет доработана данная механика.

4. (???) Атакует юнитов. Атака происходит по принципу атаки юнитов - а именно, в когда вражеский объект попадает в радиус атаки (AttackRadius), то раз в определенное количество времени DamageRate наносим урон противнику (нанести урон - вычесть из параметра здоровья противника параметр Damage). Атакуем объект так же по принципу приоритета - вражеский юнит > вражеского MotherShip > вражеской базы.

5. (??? ) Повышает свой уровень. Если игрок два раза кликает на базу - уровень базы повышается, а точнее повышаются все связанные параметры. Возможно повышается максимальное число юнитов в базе (MaxCountUnit), максимальное число здоровья самой базы, возможно что-то еще.

### Конфигурационный файл
```javascript
    "Base": 
    {
        "maxHP": 50.0, //- максимальное ХП
        "SpawnRate": 3.0, //- время, через которое юнит будет заспавлен у базы (только осле того, как пользователь отправил корабли в другую базу
        "ProduceUnitRate": 1.0, //- время, через которое база произведет юнита
        "MaxUnit": 10 //- максимальное число юнитов, которое может быть в базе (заходить может сколько угодно, но производиться не больше)
        "Damage": 3.0, //- дамаг наносимый одним выстрелом
        "DamageRate": 1.0, //- время перезарядки (в секундах)
        "AttackRadius": 4.0 //- радиус вокруг базы, в котором база умеет атаковать
        "StartCountUnit": 5 - //- количество юнитов в базе на старте
    }
```

## MotherShip

### Словесное описание
MotherShip - боевая единица игрока, существует на поле в единственном экземпляре. Выполняет почти все те же функци, что и юнит - содержит похожие поведенчиские правила, так же атакует и так же расходует здоровье, отличается от юнита лишь способом перемещения.
Перемещение MotherShip выполняется по следующему алгоритму: есть два типа перемещения - у базы (будь то дружественная или вражеская) и перелет между базами. У базы MotherShip кружит вокруг по орбите = AttackRadius, если база вражеская - соответсвенно атакует орбиту. Перемещение от базы к базу выполняется по указу игрока: перемещаемся когда игрок указал базу для перемещения, когда подлетаем к цели, садимся на орбиту, во время передвижения так же может атаковать.

### Конфигурационный файл
```javascript
   "MotherShip": {
        "maxHP": 100.0, //- максимальное ХП
        "Damage": 1.0,//- дамаг наносимый одним выстрелом
        "DamageRate": 1.0,//- время перезарядки (в секундах)
        "AttackRadius": 5.0,//- радиус вокруг базы, в котором база умеет атаковать
        "maxSpeed": 2.0 //- максимально развиваемая скорость
    }
```
