# m4dOFF
m4dOFF позволяет автоматизировать создание машиночитаемых доверенностей (МЧД). Для автоматизации данного процесса создается специальный шаблон с расширением .py в котором прописывается логика обработки .docx файла. В m4dOFF можно передать сразу с десяток файлов .docx и он из всех них сформирует МЧД по выбранному вами шаблону.

## Встроенные функции шаблона
Позволяет получить данные из таблицы.
- название свойства в таблице (property)
- индекс таблицы (отсчет с 0) (index_table)
- номер строки (index_row, по умолчанию равен 0) - нужен в случаях когда в таблице есть одинаковые поля например ФИО, тогда если выставить index_row = 1, первое значение ФИО пропустит, а второе получит.
- номер столбца (отсчет с 0) (index_cell, по умолчанию равен -1 - последнему значению в строке)
- номер свойства в текущей строке (idex_property, по умолчанию равен 0 - первому столбцу в таблице)
```python
self.parser.get_value(property, index_table, index_row = 0, index_cell = -1, idex_property = 0)
```
## Свой шаблон
Для создания шаблона необходимо:
- Создать файл [имя файла].py
- Скопировать содержимое template.py
- Написать свою логику обработки таблиц из .docx файла.

Обработка таблиц пишется просто.
> __from_string - позволяет вставлять "куски" МЧД и форматировать их не создавая сложную логику.
```python
 def __from_string(self, text: str):
    xml_string = re.sub('>[\s\n\r]+<', '><', text)
    return ET.fromstring(xml_string)
```
> self.uid - уникальный идентификатор МЧД
```python
self.uid
```
> в функции custom пишется логика обработки .docx файла.
```python
def custom(self):
    '''
    данная часть модифицируется
    :return:
    '''
    document = ET.SubElement(self.root, 'Документ') # создаем xml элемент
    trust = ET.SubElement(document, 'Довер') # создаем xml элемент
    trust.append(self.__from_string(f'''<СвДов ВидДовер="1" ПрПередов="1" ВнНомДовер="{self.parser.get_value("Внутренний номер", 0)}" НомДовер="{self.uid}" ДатаВыдДовер="{self.parser.get_value("Дата выдачи", 0)}" СрокДейст="{self.parser.get_value("Срок действия", 0)}">
        <СведСист>https://m4d.nalog.gov.ru/EMCHD/check-status?guid={self.uid}
        </СведСист>
    </СвДов>'''))
```
> self.parser.get_value("[имя первого столбца в таблице]", [номер таблицы в .docx файле (отсчет начинается с 0)])
```python
self.parser.get_value("Дата выдачи", 0)
```
