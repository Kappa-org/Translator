#Kappa:Translator

Nette Translator (c) Patrik Votoček (Vrtak-CZ), 2010 (http://patrik.votocek.cz)


Note
========
This is short manual how to use Nette Translator in the newest Nette 2.0 in its most simple version.
No need to edit or operate with .po/.mo files required. Written 2012-02-10.

Actual info/manual: http://wiki.nette.org/cs/cookbook/zprovozneni-prekladace-nettetranslator


### 1. Enable Translator

app/config/services.neon:
```neon
services:
	translator:
		factory: \Kappa\Localization\Translator\Gettext::getTranslator
		setup:
			- addFile(%appDir%/lang, front) # at leas one file required
			- Kappa\Localization\Translator\Panel::register # panel to debug bar
```

### 2. Use in templates

template

```smarty
{_"Dog"}
{_"Cat", $number} // for plural, default are Czech plurals: 1, 2-4, 5+
```

### 3. Use in forms
```php

/**
 * @autowire
 * @var \Kappa\Localization\Translator\Gettext
 */
protected $translator;

createComponentMyForm ()
{
	$form = new Form;
	// ...
	$form->setTranslator($this->translator);
}
```

###4. Use in Presenters

app/[*Module/ (presenters/)]BasePresenter.php

```php
/**
 * @persistent
 */
public $lang;

/**
 * @autowire
 * @var \Kappa\Localization\Translator\Gettext
 */
protected $translator;

/**
 * @autowire
 * @var \Nette\Http\Request
 */
protected $request;

public function startup()
{
	parent::startup();
	if (!$this->lang)
	{
		$lang = $this->request->detectLanguage(array('en', 'cs')) ?: 'cs';
		$this->redirectUrl($lang);
	}
}

/**
 * @param null $class
 * @return \Nette\Templating\ITemplate
 */
public function createTemplate($class = NULL)
{
	$template = parent::createTemplate($class);
	$this->translator->setLang($this->lang); // set lang
	$template->setTranslator($this->translator);
	return $template;
}
```

###Route:
```php
$router[] = new Route('<lang=cs>/<presenter>/<action>[/<id>]', 'Homepage:default');
```