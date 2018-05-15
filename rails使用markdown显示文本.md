## rails使用markdown显示文本
* 在Gemgfile中

```ruby
# markdown
gem 'redcarpet'
# # 代码颜色高亮
gem 'coderay'
```
* 执行 `bundle install` 安装包
* 在`application_helper`中添加如下代码

```ruby
class CodeRayify < Redcarpet::Render::HTML
    def block_code(code, language)
      language="c" if language=="c++"
      language ||= :plaintext
      begin
        CodeRay.scan(code, language).div
      rescue Exception => e
        language="markdown"
        retry
      end
    end
  end

  def markdown(text)
    coderayified = CodeRayify.new(:filter_html => true,
                                  :hard_wrap => true)
    render_options = {
      # will remove from the output HTML tags inputted by user
      filter_html:     true,
      # will insert <br /> tags in paragraphs where are newlines
      # (ignored by default)
      hard_wrap: true,
      # hash for extra link options, for example 'nofollow'
      link_attributes: { rel: 'nofollow' }
      # more
      # will remove <img> tags from output
      # no_images: true
      # will remove <a> tags from output
      # no_links: true
      # will remove <style> tags from output
      # no_styles: true
      # generate links for only safe protocols
      # safe_links_only: true
      # and more ... (prettify, with_toc_data, xhtml)
    }
    renderer = Redcarpet::Render::HTML.new(render_options)

    extensions = {
      #will parse links without need of enclosing them
      autolink:     true,
      # blocks delimited with 3 ` or ~ will be considered as code block.
      # No need to indent.  You can provide language name too.
      # ```ruby
      # block of code
      # ```
      fenced_code_blocks: true,
      # will ignore standard require for empty lines surrounding HTML blocks
      lax_spacing:  true,
      # will not generate emphasis inside of words, for example no_emph_no
      no_intra_emphasis:  true,
      # will parse strikethrough from ~~, for example: ~~bad~~
      strikethrough:      true,
      # will parse superscript after ^, you can wrap superscript in ()
      superscript:  true
      # will require a space after # in defining headers
      # space_after_headers: true
    }
    Redcarpet::Markdown.new(coderayified, extensions).render(text).html_safe
  end
```

* 在view中要显示markdown文本的地方添加`<%= markdown(text)%>`