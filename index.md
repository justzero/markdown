<script id="tmpl-index" type="text/tmpl">
<h2>首页</h2>
<ul>
<% _.each(list, function (item) { %>
    <li>
    <%=item.mtime%> - 
    <a href="/markdown?p=<%=item.name%>" target="_blank"><%=item.name%></a>
    </li>
<% }) %>
</ul>
</script>
