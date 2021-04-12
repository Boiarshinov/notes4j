---
title: "GUI"
tags:
draft: false
---

Для создания GUI в Java используются различные фреймворки:

- AWT
- [Swing](swing.md)
- JavaFX

## События

При взаимодействии с пользователем формируется событие. События представлены объектами, классы которых определены в пакете java.awt.event:

- `MouseEvent` - событие по нажатию кнопки мыши
- `KeyEvent` - событие по нажатию кнопки на клавиатуре
- `WindowEvent` -
- `ActionEvent` -
- `ItemEvent` - происходит при нажатии/отжатии флажков
- ...

### Источники событий

Источниками событий в GUI являются интерактивные элементы, с которыми может взаимодействовать пользователь

### Обработка событий
Для каждого типа событий есть свой слушатель:

| **Событие** | **Интерфейс-слушатель** |
| --- | --- |
| `MouseEvent` | `MouseListener` |
| `KeyEvent` | `KeyListener` |
| `WindowEvent` | `WindowListener` |
| `ActionEvent` | `ActionListener` |
| `ItemEvent` | `ItemListener` |
| `ListSelectionEvent` | `ListSelectionListener` |

Слушатели добавляются к элементам GUI

### Интерфейс MouseListener
Методы:
- `mousePressed()` - вызывается при нажатии кнопки мыши
- `mouseReleased()` - вызывается при отпускании кнопки мыши
- `mouseMoved()` - вызывается при перемещении мыши

### Интерфейс ActionListener

Интерфейс относится к функциональным, т.к. в нем определен всего один метод, а значит может быть заменен на [Лямбда-выражения](stream_and_lambda/lambda_expressions.md)

- actionPerformed(ActionEvent) - метод, в теле которого нужно реализовать свою логику

### Интерфейс ItemListener

Интерфейс относится к функциональным, т.к. в нем определен всего один метод, а значит может быть заменен на [Лямбда-выражения](stream_and_lambda/lambda_expressions.md)

- `itemStateChanged(ItemEvent)` - метод, в теле которого нужно реализовать свою логику

### Интерфейс ListSelectionListener

Интерфейс относится к функциональным, т.к. в нем определен всего один метод, а значит может быть заменен на [Лямбда-выражения](stream_and_lambda/lambda_expressions.md)

- `void valueChanged(ListSelectionEvent)` - метод, в теле которого нужно реализовать свою логику

### Интерфейс KeyListener
Методы:
- `keyPressed(KeyEvent)` -
- `keyReleased(KeyEvent)` -
- `keyTyped(KeyEvent)` -