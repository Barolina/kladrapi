КЛАДР в облаке
==============

Исходный код сервиса ["КЛАДР в облаке"] [1]

Технологии
----------

* [Phalcon] [3]
* [MongoDB] [4]
* [Memcached] [5]

Общая структуктура
---------

* */apps/core* - Модуль сервиса [kladr-api.ru/api.php] [2]
* */apps/frontend* - Модуль сайта [kladr-api.ru] [1]
* */docs* – PHPDoc документация сервиса
* */examples* – Примеры
* */files* – Файлы КЛАДР для загрузки в БД (урезанные варианты)
* */loader* – Загрузчик файлов КЛАДР в БД
* */tests* - Функциональные тесты
* */public*

Структура сервиса
-----------------

* */apps/core/config/* - Файлы конфигурации сервиса
* */apps/core/controllers/* - Контроллеры
* */apps/core/models/* - Модели ODM
* */apps/core/plugins/base/* - Базовые классы плагинов
* */apps/core/plugins/general/* - Плагины
* */apps/core/plugins/tools/* - Вспомогательные классы
* */apps/core/services/* - Сервисы

Архитектура
-----------

Архитектура сервиса построена на плагинах. 
Для того чтобы расширить функциональность надо всего лишь реализовать
плагин и подключить его в модуле сервиса.

**Пример:**
Добавим плагин возвращающий количество объектов в результате.

*Добавил плагин CountPlugin.php в /apps/core/plugins/general/*

`````php
<?php

namespace Kladr\Core\Plugins\General {

    use \Phalcon\Http\Request,
        \Phalcon\Mvc\User\Plugin,
        \Kladr\Core\Plugins\Base\IPlugin,
        \Kladr\Core\Plugins\Base\PluginResult;

    class CountPlugin extends Plugin implements IPlugin
    {
        public function process(Request $request, PluginResult $prevResult) 
        {
            if($prevResult->error){
                return $prevResult;
            }

            $result = $prevResult;
            $result->result = count($result->result);    
            return $result;
        }        
    }
}
`````

*Подключим плагин в /apps/core/Module.php*

`````php
// Register CountPlugin
$di->set('count', function(){
    return new \Kladr\Core\Plugins\General\CountPlugin();
});

// Setting api
$di->setShared('api', function() use ($di) {
    $api = new Services\ApiService();
    $api->addPlugin($di->get('validate'));
    $api->addPlugin($di->get('find'));   
    $api->addPlugin($di->get('findParents'));
    $api->addPlugin($di->get('count')); // add CountPlugin
    return $api;
});
`````

Лицензия
--------

Проект распространяетс под лицензией «MIT».

Copyright (c) 2013 «Праймпикс»

Данная лицензия разрешает лицам, получившим копию данного программного 
обеспечения и сопутствующей документации (в дальнейшем именуемыми «Программное 
Обеспечение»), безвозмездно использовать Программное Обеспечение без ограничений, 
включая неограниченное право на использование, копирование, изменение, добавление, 
публикацию, распространение, сублицензирование и/или продажу копий Программного 
Обеспечения, также как и лицам, которым предоставляется данное Программное 
Обеспечение, при соблюдении следующих условий:

Указанное выше уведомление об авторском праве и данные условия должны быть 
включены во все копии или значимые части данного Программного Обеспечения.

ДАННОЕ ПРОГРАММНОЕ ОБЕСПЕЧЕНИЕ ПРЕДОСТАВЛЯЕТСЯ «КАК ЕСТЬ», БЕЗ КАКИХ-ЛИБО 
ГАРАНТИЙ, ЯВНО ВЫРАЖЕННЫХ ИЛИ ПОДРАЗУМЕВАЕМЫХ, ВКЛЮЧАЯ, НО НЕ ОГРАНИЧИВАЯСЬ 
ГАРАНТИЯМИ ТОВАРНОЙ ПРИГОДНОСТИ, СООТВЕТСТВИЯ ПО ЕГО КОНКРЕТНОМУ НАЗНАЧЕНИЮ И 
ОТСУТСТВИЯ НАРУШЕНИЙ ПРАВ. НИ В КАКОМ СЛУЧАЕ АВТОРЫ ИЛИ ПРАВООБЛАДАТЕЛИ НЕ НЕСУТ 
ОТВЕТСТВЕННОСТИ ПО ИСКАМ О ВОЗМЕЩЕНИИ УЩЕРБА, УБЫТКОВ ИЛИ ДРУГИХ ТРЕБОВАНИЙ ПО 
ДЕЙСТВУЮЩИМ КОНТРАКТАМ, ДЕЛИКТАМ ИЛИ ИНОМУ, ВОЗНИКШИМ ИЗ, ИМЕЮЩИМ ПРИЧИНОЙ ИЛИ 
СВЯЗАННЫМ С ПРОГРАММНЫМ ОБЕСПЕЧЕНИЕМ ИЛИ ИСПОЛЬЗОВАНИЕМ ПРОГРАММНОГО ОБЕСПЕЧЕНИЯ 
ИЛИ ИНЫМИ ДЕЙСТВИЯМИ С ПРОГРАММНЫМ ОБЕСПЕЧЕНИЕМ.


[1]: http://kladr-api.ru/         "КЛАДР в облаке"
[2]: http://kladr-api.ru/api.php  "Сервис"
[3]: http://phalconphp.com/       "Phalcon"
[4]: http://www.mongodb.org/      "MongoDB"
[5]: http://memcached.org/        "Memcached"