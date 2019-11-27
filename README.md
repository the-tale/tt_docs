
# Техническая документация «Сказки»

В этом проекте собрана техническая документация по проекту:

- внешнее API;
- архитектура;
- геймдизайн (то, что 100% устаканилось)
- всё, что может быть похоже на документацию ;-)

**Мы всегда рады любой помощи!**

Улучшение документации — отличный способ сделать первый шаг в разработке «Сказки»!

Любые вопросы можно задать на [форуме игры](http://the-tale.org/forum/subcategories/28).

Актуальная документация всегда опубликована тут: http://docs.the-tale.org/ (от изменений до их появления проходит несколько минут).

Документация ведётся в формате [reStructuredText](https://ru.wikipedia.org/wiki/ReStructuredText) генерируется с помощью [Sphinx](http://www.sphinx-doc.org).

[Документация по reStructuredText на английском](http://docutils.sourceforge.net/docs/user/rst/quickref.html)

[Документация по reStructuredText на русском](https://www.komtet.ru/lib/plangs/python/vvedenie-v-rst-formatirovanie)

## Генерация документации

```
cd ./my-project/
pip install -r requirements.txt

cd ./docs
make html
```

#### Откройте документацию в браузере
GNU/linux ``` $ xdg-open ./build/html/index.html```  
macOS ```$ open ./build/html/index.html```  
Windows - откройте `./build/html/index.html` в вашем браузере
