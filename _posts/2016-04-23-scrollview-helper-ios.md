---
layout: post
title: UIScrollView helper iOS
excerpt: "Bounce, scrollview. It's just U and I."
categories: [desenvolvimento]
tags: [ios, objective-c]
comments: true
---

### Função facilitadora para tela de todos os tamanhos
No momento estou trabalhando em um projeto onde o `autoLayout` não está sendo usado por razões de compatibilidade de versões iOS mais antigas, e isto não facilita a criação de layout. Mas claro, nem tudo é um pesadelo quando o **StackOverflow** te abraça. Ao procurar por uma opção factível de scrollView me deparei com [este post](http://stackoverflow.com/questions/2944294/how-i-auto-size-a-uiscrollview-to-fit-the-content) que adiantou meu trabaho em grande parte. A sacada desta resposta é a seguinte: Fazer um loop em todos os `subviews` do seu `scrollView` e montar um `CGRect`(*CGRect vem de CGRectangle*) com a altura apropriada do scroll para o seu `contentSize` caber todos os itens da tela sem advinhações. Com isto, pegamos o tamanho da `NavigationBar` e `TabBar` (Não é necessário, mas caso a sua UIView esteja calculada para encher a tela, o scroll vai calcular a área inteira e ignorar a `nav/tab`) para saber se é necessário dar o scroll.

Agora, suponha que você tenha na sua `interface` referente à esta `implementation`, uma variável chamado `scrollView` referenciando o scroll da sua página. De início, ela não terá seu `contentSize` montado, então o valor é tomado a partir dos `subviews`. Logo em seguida, caso o tamanho do scroll menos a área da `nav/tab` da tela seja maior que o conteúdo exibido, é necessário que o scroll aconteca. Caso contrário, a tela poderá se manter estática para um *user experience* um pouco mais adequado.

> *_scrollView* e *self.scrollView* referenciam o mesmo objeto de instância

{% highlight objc%}
- (void)setScrollView {
    // Pegando os tamanhos da navbar e tabbar caso precise usar na UIView principal
    CGFloat tabSize = self.tabBarController.tabBar.frame.size.height;
    CGFloat navSize = [self navigationController].navigationBar.frame.size.height;
    CGFloat NAV_TAB_SIZE = tabSize + navSize;

    // Loop nos subviews
    CGRect contentRect = CGRectZero;
    for (UIView *view in self.scrollView.subviews) {
        contentRect = CGRectUnion(contentRect, view.frame);
    }
    // Montando o tamanho do scroll
    self.scrollView.contentSize = CGSizeMake(contentRect.size.width, contentRect.size.height);

    // Verificando se o scrollview deve animar com bounce e dar scroll
    if (self.scrollView.contentSize.height - NAV_TAB_SIZE > contentRect.size.height) {
        self.scrollView.scrollEnabled = NO;
        self.scrollView.bounces = NO;
    } else {
        _scrollView.bounces = YES;
        _scrollView.scrollEnabled = YES;
    }
{% endhighlight %}

É só isso que temos sobre o scrollView hoje. Uma pequena ajuda não faz mal. Um abraço!
