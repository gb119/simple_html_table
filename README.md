# simple_html_table

Simple way to create HTML tables with support for rowspan and colspan attributes.

## Usage

simple_html_table defines three classes that represent HTML tables, rows and cells respectively. To use the module,
create a Table instance and pass it a 2D numpy array of strings, or a callable that will take Cell objects and return
strings.:

    from simple_html_table import Table

    contents = [
        ["Head 1", "Head 2", "Head 3", "Head 4"],
        ["(1,1)", "(1,2)", "(1,3)", "(1,4)"],
        ["(2,1)", "(2,2)", "(2,3)", "(2,4)"],
    ]

    table = Table((3, 4), table_contents=np.array(contents))

    table[1, 1].colspan = 2
    table[0, 0].rowspan = 3
    table[0, 1].attrs_dict.update({"style": "{ background: red; }"})
    table[0].header = True
    for cell in table[0]:
        cell.classes = "table_th"

    result = table.render()
    print(result,"\n")

    def content_func(cell: Cell) -> str:
        """Callback function to fill in the table."""
        row, col = cell.location
        return contents[row][col]

    table2 = Table((3, 4), table_contents=content_func)
    result = table2.render()
    print(result)


### Output

>
    <table class='html_table'>
    	<tr class='html_tr'>
    		<th class='table_th' rowspan=3>Head 1</th>
    		<th class='table_th' style='{ background: red; }'>Head 2</th>
    		<th class='table_th'>Head 3</th>
    		<th class='table_th'>Head 4</th>
    	</tr>
    	<tr class='html_tr'>
    		<td class='html_td' colspan=2>(1,2)</td>
    		<td class='html_td'>(1,4)</td>
    	</tr>
    	<tr class='html_tr'>
    		<td class='html_td'>(2,2)</td>
    		<td class='html_td'>(2,3)</td>
    		<td class='html_td'>(2,4)</td>
    	</tr>
    </table>

    <table class='html_table'>
    	<tr class='html_tr'>
    		<td class='html_td'>Head 1</td>
    		<td class='html_td'>Head 2</td>
    		<td class='html_td'>Head 3</td>
    		<td class='html_td'>Head 4</td>
    	</tr>
    	<tr class='html_tr'>
    		<td class='html_td'>(1,1)</td>
    		<td class='html_td'>(1,2)</td>
    		<td class='html_td'>(1,3)</td>
    		<td class='html_td'>(1,4)</td>
    	</tr>
    	<tr class='html_tr'>
    		<td class='html_td'>(2,1)</td>
    		<td class='html_td'>(2,2)</td>
    		<td class='html_td'>(2,3)</td>
    		<td class='html_td'>(2,4)</td>
    	</tr>
    </table>

Table and Row objects implement mutable sequence interfaces, so can be indexed. A Table will except being indexed with
a single integer to return a Row or with a tuple of two integers to return a Cell. A Row can be indexed to return Cell
instances.

Cells know which Row they belong to and Tows know which Table they belong to. This means copying Cells and Rows will
probably mess up unless you explicitly reset the pointers.

Colpan and Rowpans are implemented simply by storing references to the same Cell objects within the Row objects - as a
result, it is always possible to index the Table, but the Cell returned may think it belongs to a different row and
column within the table.
