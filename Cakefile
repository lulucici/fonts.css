fs = require 'fs'
yaml = require 'js-yaml'
_ = require 'underscore'

families =
  黑体: "hei"
  宋体: "song"
  楷体: "kai"
  仿宋: "fang-song"
  明体: "ming"

weights =
  Normal: "normal"
  细: "light"
  中: "medium"
  超细: "ultra-light"

task "build", ->
  fs.readFile 'fonts.yml', {encoding: 'UTF-8'}, (err, data) ->
    throw err if err
    # Parse YAML
    fonts = yaml.load data
    # fill property with defaults
    fonts = fonts.map (elem) -> _.defaults elem, {weight: "Normal"}
    # Collect fonts
    collections = []
    for family in _.keys(families)
      for weight in _.keys(weights)
        results = fonts.filter (font) -> font.family is family and font.weight is weight
        platform = results.map (elem) -> elem.platform
        alias = _.flatten(results.map (elem) -> elem.alias)
        collection =
          fonts: alias
          names: results.map (elem) -> elem.name
          header: if weight? then "#{family}（#{weight}）" else family
          class: "font-#{families[family]}-#{weights[weight]}"
          css: "font-family: \"#{alias.join('\", \"')}\";"
          platform: _.compact(_.uniq(_.flatten(platform)))
        collections.push collection
    # filter empty collections
    collections = collections.filter (collection) -> collection.fonts.length > 0
    # generate fonts.css
    console.log "Generating fonts.css"
    css = collections.map (collection) -> ".#{collection.class} {#{collection.css}}"
    fs.writeFile 'fonts.css', css.join("\n"), (err) -> throw err if err
    # generate index.html
    console.log "Generating index.html"
    collections = collections.map (collection) ->
      platform = collection.platform.map (platform) -> "<li>#{platform}</li>"

      "\n     <div class=\"collection\">\n
        <div class=\"tags\">#{platform.join('')}</div>\n
        <div class=\"text #{collection.class}\">\n
          <h2>#{collection.header}</h2>\n
          <ul>\n
            <li class=\"size18\">故天将降大任于是人也必先苦其心智劳其筋骨饿其体肤空乏其身行弗乱其所为所以动心忍性曾益其所不能。<li>\n
            <li class=\"size16\">故天将降大任于是人也必先苦其心智劳其筋骨饿其体肤空乏其身行弗乱其所为所以动心忍性曾益其所不能。<li>\n
            <li class=\"size14\">故天将降大任于是人也必先苦其心智劳其筋骨饿其体肤空乏其身行弗乱其所为所以动心忍性曾益其所不能。<li>\n
            <li class=\"size12\">故天将降大任于是人也必先苦其心智劳其筋骨饿其体肤空乏其身行弗乱其所为所以动心忍性曾益其所不能。<li>\n
          </ul>\n
        </div>\n
        <div class=\"name\">#{collection.names.join('，')}</div>\n
        <div class=\"css\">Class: #{collection.class}</div>\n
        <div class=\"css\">#{_.escape(collection.css)}</div>\n
      </div>\n"
    html = "<!doctype HTML>\n
<head>\n
  <meta charset=\"UTF-8\">\n
  <link rel=\"stylesheet\" type=\"text/css\" href=\"fonts.css\" />\n
  <link rel=\"stylesheet\" type=\"text/css\" href=\"styles.css\" />\n
</head>\n
<body>\n
  <header>Fonts.css -- 跨平台中文字体解决方案</header>\n
  <article>#{collections.join('')}</article>\n
</body>"
    fs.writeFile 'index.html', html, (err) -> throw err if err