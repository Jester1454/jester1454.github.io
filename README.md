# Краткое Описание полей Sea Battle
Есть три типа объекта - Unit, Base, Tower, для каждой из сторон (для Unit и Tower - две, для Base + нейтральная) все параметры 
выделены в отдельный блок, который соответственно назван.

Для юнита есть следующие параметры
    "Unit":
    {
        "maxHP": 15.0, - максимальное ХП
        "Damage": 5.0, - дамаг наносимый одним выстрелом
        "DamageRate": 1.0, - время перезарядки (в секундах)
        "AttackRadius": 3.0, - радиус вокруг корабля, в котором корабль умеет атаковать
        "maxSpeed": 2.5 - максимально развиваемая скорость
    }
    
Для базы есть следующие параметры
    "Base": 
    {
        "maxHP": 50.0, - максимальное ХП
        "SpawnRate": 3.0, - время, через которое юнит будет заспавлен у базы (только осле того, как пользователь отправил корабли в другую базу
        "ProduceUnitRate": 1.0, - время, через которое база произведет юнита
        "MaxUnit": 10 - максимальное число юнитов, которое может быть в базе (заходить может сколько угодно, но производиться не больше)
    }
Для башни есть следующие параметры 
    "Tower": 
    {
        "maxHP": 15.0, - максимальный запас здоровья
        "Damage": 3.0, - дамаг наносимый одним выстрелом 
        "DamageRate": 1.0, - время перезарядки (в секундах)
        "AttackRadius": 5.0, - радиус вокруг башни, в котором корабль умеет атаковать
        "RotateSpeed": 2.0 - максимальная скорость вращения башни
    }
