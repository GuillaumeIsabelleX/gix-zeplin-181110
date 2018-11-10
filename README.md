# gix-zeplin-181110
## @stcgoal  181110102038 An extension to integrate visual design into coding

### @d 18111011

## @a Prepare the Project

npm install zeplin-extension-style-kit
npm install request
npm install net
npm install tls
npm install fs


## @a Migrate experimental code from my-zeplin-x-181026


function layer(context, selectedLayer) {
  const params = /* */
    getParams(context);
  const cssGenerator = createGenerator(context.project, params);

  const l = /* Create a New layer with the current one */
    new Layer(selectedLayer);

  const layerRuleSet = l.style; // Get the Style of the Layer
  const childrenRuleSet = [];
  const { defaultTextStyle } = selectedLayer;

  //Layer is Type Text and has default style.
  if (selectedLayer.type === "text" && defaultTextStyle) {
    const declarations = l.getLayerTextStyleDeclarations(defaultTextStyle);

    declarations.forEach(p => layerRuleSet.addDeclaration(p));

    getUniqueLayerTextStyles(selectedLayer).filter(
      textStyle => !defaultTextStyle.equals(textStyle)
    ).forEach((textStyle, idx) => {
      childrenRuleSet.push(
        new RuleSet(
          `${selectorize(selectedLayer.name)} ${selectorize(`text-style-${idx + 1}`)}`,
          l.getLayerTextStyleDeclarations(textStyle)
        )
      );
    });
  } //End If selected layer is TEXT

  //CSS Style for the Layer using the CSS Generator
  const layerStyle = cssGenerator.ruleSet(layerRuleSet);
  const childrenStyles = childrenRuleSet.map(
    s => cssGenerator.ruleSet(s, { parentDeclarations: layerRuleSet.declarations })
  );
  var layerSuffix = "__c";
  var layerDomID =
    selectedLayer.name + layerSuffix;



  //@v An X to see what I can generate with it.
  var xtra = "/* jg (layer)  1810310922*/"
    + "\n\n"
    + "/*"
    + "@starthtml\n"
    + "<div id=\"" + layerDomID
    + "\"></div>"
    + "\n"
    + "@endhtml\n"
    + "*/"
    + "\n"
    + "#" + layerDomID + " {\n"
    + "/* Custom CSS for ID " + layerDomID + "*/"
    + "\n}\n"
    + "\n\n"
    + "/* Width Test " +
    selectedLayer.rect.width
    + "*/"

    ;
  console.log("returning data for layer: " + layerDomID);

  return {
    code: [layerStyle + xtra
      , ...childrenStyles].join("\n\n/* JGI Generated - layer */\n"

      ),
    language: LANG
  };
}
