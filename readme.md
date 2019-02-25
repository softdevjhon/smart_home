# Smart home
Данный проект является завершающим проектом в курсе "Программирование web-сервисов на Python". Для корректного запуска веб-приложения обязательно запустите миграции и примените их, а также поменяйте настройки почты. Данное приложение взаимодействует с API умного дома и продумывает всю функциональность в зависимости от событий и действий пользователя.

Документация по api умного дома: http://smarthome.t3st.ru/docs
Техническое задание по которому было выполнено данное приложение предоставляется внизу:

Устройства, подключенные к контроллеру, доступны на запись (обычно true - включить/открыть, false - выключить/закрыть, но бывают варианты). И датчики и устройства доступны на чтение. Устройства, при чтении с них, работают как датчики и возвращают свое состояние, которое может отличаться от записанного.

# Устройства (запись):
air_conditioner – Кондиционер (true – вкл, false – выкл). При включении постепенно понижает температуру в спальне, пока она не достигнет 16 градусов и сильнее охладить уже не может.
bedroom_light – Лампа в спальне (true – вкл, false – выкл).
bathroom_light – Лампа в ванной (true – вкл, false – выкл).
curtains – Занавески string (“open” – открыть, “close” – закрыть).
boiler – Бойлер (true – вкл, false – выкл). При включении постепенно повышает температуру воды, пока она не достигнет 90 градусов. Для работы должен быть открыт входной кран холодной воды.
cold_water – Входной кран холодной воды (true – открыть, false – закрыть). Позволяет открыть/перекрыть подачу холодной воды в квартиру
hot_water – Входной кран горячей воды (true – открыть, false – закрыть).
washing_machine – Стиральная машина string (“on” – вкл, “off” – выкл). При включении начинает стирать, потом самостоятельно отключается. Может сломаться и протечь.

# Датчики (чтение):
air_conditioner – Кондиционер. (true – вкл, false – выкл).
bedroom_temperature – Температура в спальне. Int (0 – 80).
bedroom_light – Лампа в спальне. (true – вкл, false – выкл).
smoke_detector – Датчик задымления на потолке. (true – задымление, false – нет).
bedroom_presence – Датчик присутствия в спальне. (true – есть человек, false – нет).
bedroom_motion – Датчик движения в спальне. (true – есть движение, false – нет).
curtains – Занавески. string (“open” – открыты, “close” – закрыты, “slightly_open” – приоткрыты вручную).
outdoor_light – Датчик освещенности за окном (0 – 100).
boiler – Бойлер. (true – вкл, false – выкл).
boiler_temperature – Температура горячей воды бойлере. Int (0 – 100 / null). Если перекрыта холодная вода, то воды в бойлере нет, и датчик возвращает null.
cold_water – Входной кран холодной воды. (true – открыт, false – закрыт).
hot_water – Входной кран горячей воды. (true – открыт, false – закрыт).
bathroom_light – Лампа в ванной. (true – вкл, false – выкл).
bathroom_presence – Датчик присутствия в ванной. (true – есть человек, false – нет).
bathroom_motion – Датчик движения в ванной. (true – есть движение, false – нет)
washing_machine – Стиральная машина. string (“on” – вкл, “off” – выкл, “broken” – сломана).
leak_detector – Датчик протечки воды (true – протечка, false – сухо).


# Реакция на события:
Если есть протечка воды (leak_detector=true), закрыть холодную (cold_water=false) и горячую (hot_water=false) воду и отослать письмо в момент обнаружения.
Если холодная вода (cold_water) закрыта, немедленно выключить бойлер (boiler) и стиральную машину (washing_machine) и ни при каких условиях не включать их, пока холодная вода не будет снова открыта.
Если горячая вода имеет температуру (boiler_temperature) меньше чем hot_water_target_temperature - 10%, нужно включить бойлер (boiler), и ждать пока она не достигнет температуры hot_water_target_temperature + 10%, после чего в целях экономии энергии бойлер нужно отключить
Если шторы частично открыты (curtains == “slightly_open”), то они находятся на ручном управлении - это значит их состояние нельзя изменять автоматически ни при каких условиях.
Если на улице (outdoor_light) темнее 50, открыть шторы (curtains), но только если не горит лампа в спальне (bedroom_light). Если на улице (outdoor_light) светлее 50, или горит свет в спальне (bedroom_light), закрыть шторы. Кроме случаев когда они на ручном управлении
Если обнаружен дым (smoke_detector), немедленно выключить следующие приборы [air_conditioner, bedroom_light, bathroom_light, boiler, washing_machine], и ни при каких условиях не включать их, пока дым не исчезнет.
Если температура в спальне (bedroom_temperature) поднялась выше bedroom_target_temperature + 10% - включить кондиционер (air_conditioner), и ждать пока температура не опустится ниже bedroom_target_temperature - 10%, после чего кондиционер отключить.

Проверка событий запускается после отправки формы и описана в отдельном файле coursera_house/core/tasks.py


