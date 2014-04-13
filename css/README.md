This document outlines a recommendation for developing in CSS for UI developers. The recommendation is based on SMACSS and BEM. 

SMACSS is a design pattern/methodology for CSS, and stands for Scalable and Modular Architecture for CSS. It lets you increase the semantics and modularity of your CSS by categorizing rules into distinct sets. 

BEM is a naming convention methodology in the same vein, and stands for Block-Element-Modifier.


Definitions
----

*Semantics*: Make the HTML and CSS meaningful and clear based on its content and purpose

*Modularity*: Decrease the expectation of a specific HTML structure. A module should be able to move and live on any part of any page.

  

Recommendation 1: Categorize All CSS Rules by Type
====
  

Base Rules
----
resets, HTML tag rules; no IDs or classes

Layout Rules
----
major, global styles like header and footer; only set to use IDs

Pattern Rules
----
reusable, repeated library of components; uses classes

Module Rules
----
unique and non-global components; uses classes, and --modifier as needed to tweak a few properties for special cases, except when modification is result of a user action

Element Rules
----
The smallest individual parts of a module. These rules let us keep class names short so we don't have to keep sub-modularizing and keep on extending the class name. They are not meant to be reusable outside that module; since names which may not be unique, they must be scoped to that specific module.


State Rules
---
Typically indicates a change in style in response to user actions. Always begins with "is-". These classes are added to existing elements, and thus are nested, scoped to the elements they modify (via parent selector).

Can also be used as a modifier, similar to above, in cases where extends would be problematic for selecting the element with JS.


Recommendation 2: Name All CSS Selectors by Type
====
  

Layout:
----
	#l-header
	#l-content
	#l-footer
	#l-nav .p-horizontalList
  
Pattern:
----
	.p-tab
	.p-tab.is-active
	.p-carousel
	.p-carousel-slide
	.p-horizontalList
	.p-ctaButton
	.p-ctaButton.is-selected
	.p-columns
	.p-columns--wide

Modules / modifiers:
----
	.m-productDetail
	.m-homePromotion
	.m-homePromotion--featured
	.m-productThumbnail
	.m-productThumbnail--large  

Submodules:
----
	.m-recommendedProducts-product
	.m-productThumbnail-image
	.m-productDetail-social

Element:
----
	.e-title
	.e-price
	.e-price--savings
	.e-formFieldLabel
	.e-formFieldName--disabled

State:
----
	.m-homePromotion--featured.is-hovered
	.m-recommendedProducts-product.is-selected


### Abbreviations ###
Here is list of common abbreviations that we've agreed on, but is by no means comprehensive or complete. 

*category*: catList/catItem, rootCat (for the primary/first level of a dropdown), subCat (for the second level)

*navigation*: navList/navItem

*call to action*: cta

*background*: bg

*foreground*: fg

*button*: btn

*product, image*: short enough to just spell out 

(I'm not a fan of "prod" because it could mean "production")


Recommendation 3: Organize All CSS Rules in Separate Files by Type
====

With a the use of a CSS preprocessor such as Sass, all base rules would be kept in a single file, or directory of files if further splitting is desired; same with and layout rules.

Pattern rules would be in a separate file for each pattern, to be included as needed. Same with module rules + associated state rules.

### Using @extend for modifiers ###

Placing "@extend .myFirstSelector" in a ruleset for ".mySecondSelector" will apply all the styles of .myFirstSelector to .mySecondSelector. Afterwards, you can place any different or overriding styles for .mySecondSelector. In the past, one would have to maintain a long list of comma-grouped selectors to accomplish this. In the compiled CSS, both selectors will automatically be comma grouped as such (along with any other other selectors which also extend the same thing.)

A perfect use for this is for modifiers. When creating any kind of rule which is a special-case instance of another rule, and we want to use the --modifier naming convention, we can write styles like this:

	.myModule {
		font-weight: bold;
	}
	
	
	.myModule--specialCase {
		@extend .myModule;
		text-transform: uppercase;
	}

In our HTML, we only need to apply the class `.myModule--specialCase.` This way, we don't need to add multiple classes to achieve modularity.

### Using @extend for pattern rules ###

`@extend` is also quite useful to use with placeholder selectors or "silent classes", which is fantastic for creating modularity in pattern styles. A pattern style need not actually be used in HTML to be extended and re-used in many places, sort of like a small include. Just use a % in front of the ruleset where a period or hash sign might normally be. Example:

	%buttonBorders {
		@include border-radius(1em);
		border: 1px solid #f00;
	} 
	
	.p-ctaButton {
		@extend %buttonBorders;
	}

Since they never appear in output, naming conventions for silent classes are much more lax as far as semantics. That doesn't mean they shouldn't make sense to your fellow developers.  

More on this at [Treehouse](http://blog.teamtreehouse.com/extending-placeholder-selectors-with-sass)


### Using the parent selector for state rules ###

Here is an example of how to set a state rule that indicates a somewhat different style, to indicate that a user has taken an action.


	.e-option {
		border-radius: 5px;
		color: white;
		padding: 1em;
		text-align: center;
		  
		&.is-selected {
			color: red;
			border: 1px solid blue;
		}
	}
  
  
Note that the state rules only modify or add a few properties; mostly it is still inheriting.

Currently, I think this can also be used similarly to a modifier, not just in response to user action (`.is-highlighted, .is-rightAligned`), only because to select every element and all its modifiers (`.e-option, .e-option--highlighted, .e-option--someModifier, .e-option--rightAligned`, etc etc) in JS is cumbersome, and jQuery's `hasClass('.e-option')` would not work. 

It seemed like a best practice in SMACSS to use the `--modifier` approach, and have a single class name rather than adding a class name. However, I don't know how solve that JS issue; we'd have to enforce using `[class^='e-option']` instead. The jury's still out, and the rec is always evolving.


Recommendation 4: Name CSS Selectors Semantically
====
  
Key to a strong design pattern and convention is semantics: Naming things for what they are and what their purpose is, not what they look like and where they are located. Purpose generally, will never change; everything else is subject to change. We want to make names permanent and modular.

Good semantic terms:
----
main
global
home
featured
callout
content
nav / menu
error
container / wrapper
primary / secondary / tertiary  

Unsemantic terms:
---
blue / red / any color
left / right / top / bottom / sidebar / any indicator of position
small / medium / large / any descriptor of size
bold / italic / margin / spacing / any descriptor of style / anything which is also a CSS property
anything which is also an HTML element *
any increment number
any date or time reference
text / info / box / style / anything too vague

\*(unless also a general semantic descriptor)

*Good:* `.p-productList` / *Bad*: `.p-productUL`

*Good:* `.m-productDetail-subheading` / *Bad:* `.m-productDetail-H2`

*Good:* `.m-productDetail-description` / *Bad:* `.m-productDetail-paragraph`
  

### Other too-specific or non-modular terms to be avoided: ###

banner (what if we redesigned it to a square instead? Try "feature," "promotion", something that describes it purpose)

popup (what if we changed formats, and this were in-page instead of a popup? try "warning," "notification", something that describes it purpose)

endeca / certona / jQuery / fancybox / any vendor or plugin that we might someday dump

any abbreviation or shortened word that isn't extremely obvious

  
One trick is to use verbs, to understand the purpose by what it "does". This also makes for easy mapping of an element and its JS function.

### Common Action Verbs: ### 

show
hide
rotate
move
showNext
showPrev
focus
disable
calculate

### Examples ### 
.m-productList-showQuickView


Recommendation 5: Scoping Techniques
====
  
Root level scoping
----

Our starter template includes code from the HTML5Boilerplate which will add a class to the root html element based on the current version of IE. This is our chosen method for adding IE specific styles. (An IE specific CSS file may also be conditionally loaded in.)
  

	.lt-ie8 .myModule { 
		/* Modifications to spacing, position, etc based on IE8 quirks */ 
	}
	
	.lt-ie9 .myOtherModule { 
		/* A fallback for a CSS3 feature not natively supported */ 
	}

Modernizr has a similar feature which adds whether or not certain HTML5/CSS3/other new features are supported in the current browser, usable by CSS or JS.

  

.no-flexbox .myModule { 

/* an alternative non-flexbox layout */ 

}

  

$('.canvas .myModule').click(function(){

/* code to be used only if canvas is supported */

});

  

Eliminate the use of IE "hacks" such as the asterisk hack, underscore hack, etc. (*+html .m-module, _html .m-module) These are not forward compatible, are unclear, and don't play nice with preprocessors.

  
  

Template scoping

  

Though we aim to make our code as modular as possible and reusable globally, sometimes certain contexts need special overrides. A good method is to use template scoping to compartmentalize those exceptions to the rules. Thus, any page with its own template Sass file should also have an ID attached to the body tag with the same name. The CSS for this should be placed with the template Sass file, NOT the module Sass file.

  

These should be put in place regardless of whether they are in use at first development, so that in the future those exceptions are easy to add.

  

HTML: body id="template-productDetail"

in template-productDetail.scss: #template-productDetail .myModule

  
  

Scoping for sprints

One technique for avoiding the usual conflicts with CHM is to add a class specific to the team and sprint to the body tag, in which to temporarily place styles that need to overwrite CHM. It should give our work a higher cascade order when paired with the template scope.

  

HTML: body id="template-productDetail" class="team01-sprint09"

CSS: #template-productDetail.team01-sprint09

  

These must not go into production and should be removed from HTML and CSS before release.

  

Recommendation 6: Formatting

  

Comments

Besides generally commenting as profusely as you can, label each module or pattern like so:

  

/* modules / colorSwatchMenu

*

* Shows a menu on list pages to view available colors.

*

*/

  

/* patterns / verticalCenter

*

* Vertically center a child element inside its parent

*

*/

  

Remember that in Sass, double-slash style comments do not get compiled, so they are good for technical comments meant for other devs, TODOs, etc. Slash-star comments do get compiled, so they are good for labelling modules, explaining the purpose of something, or other items you'd like for the design team to see.

  

Whitespace

  

No matter your IDE/editor, set it to use "soft tabs" (spaces) for indentation. Set your "tab" size to be four spaces. Tab before all properties in a ruleset, and on any nested rulesets. Example (not exact because it’s a Google Doc):

  

.m-productThumbnail--xlarge {

@extend .m-productThumbnail;

width: #{$width-thumbnail--xlarge}px;

  

.m-productThumbnail-image {

height: #{$height-thumbnail--xlarge}px;

img {

max-height: #{$height-thumbnail--xlarge}px;

}

}

}

  

Add a blank line after every non-nested ruleset. Add a newline after every property. Do not try to compress your source files (such as Sass files) for whitespace; make them as readable as possible. Compression can be done at compile time.

  

Alphabetical Order

  

Whithin a ruleset, I recommend placing Sass "@extend"s first, followed by "@include"s, then all other CSS properties in alphabetical order. Example:

  

.m-quickView-link {

@include border-radius(5px);

background-color: #E5E7E8;

border: 1px solid #646464;

bottom: 10px; 

color: #646464;

cursor: pointer;

display: none;

height: 24px;

font-family: Verdana, Geneva, sans-serif;

font-size: #{$width-thumbnail / $quickView-fontSizeAdjustment}em;

left: 50%;

line-height: 100%;

margin-left: -44px;

padding: 4px;

position: absolute;

text-decoration: none;

width: 88px;

z-index: 999;

}

  

Even though this does separate rules which logically work together, I've found that all other methods of ordering are so subjective and prone to different interpretations, that this highly objective method is the most orderly. Firebug and Chrome Tools also display properties this way by default, making copying changes made there into your code easier.

  

Other Recommendations

  

- Minimize the use of element selectors(#l-header ul) or qualifying a selector with an element type (#l-header ul.m-navigation). Instead, create semantic selectors that can be used on any element, in the event of a redesign (#l-header .m-navigation). If what you want to style is part of an existing module, try creating an element rule (#l-header .m-navigation .e-link).   
If you have too many elements that you just can't imagine adding a class to each one, run it by your team before you do something like (#l-header .m-navigation a) and COMMENT about it. A common case for an exception is for navigation menus that can contain dozens of li or a elements, or p tags in a long page of text. 
  

- Limit to 3 levels of nesting in Sass; nest only content that is exclusively related, such as element rules, that can't possibly exist outside the container. If you have no more nesting levels left, try to submodularize (.m-module-subModule) and scope to that instead. Or, start a whole new module from a smaller portion of the larger module.   
Don't explicitly nest inside a wrapper unless you have to; remember that we're not trying to mimic HTML structure in CSS. (.m-mySwatchModule .e-swatch works better than .m-mySwatchModule .m-mySwatchModule-swatchWrapper .e-swatch) 
  

- When in doubt when creating new selector names, ask your teammates if it makes sense. 
  

- If you'd like to create a new Sass file for a new module or pattern that you're writing, note this to your team. Follow the existing format as far as commenting, whitespace, etc. In the rare event that you're creating an all new template, and you'd need a new template Sass file, discuss this with everyone first. 
  

- If you need to write a media query specific style, place it inline with the rest of the rules, as if it were just another property (or as close to the default rules as you can). There will be no separate media-query-only or per-breakpoint file. Use the Sass Breakpoint plugin. 
  

- Above all, the methodology should make intuitive sense for all current and future developers. Write it with pride, so that you don't have to be "that person" that everyone shakes their head at when they read your code in five years. 
  

Further Reading

[http://smacss.com/](http://smacss.com/)

[http://bem.info/method/](http://bem.info/method/)

[http://alchemyindesign.com/notes/2012/10/03/smacss-notes-on-usage.html](http://alchemyindesign.com/notes/2012/10/03/smacss-notes-on-usage.html)