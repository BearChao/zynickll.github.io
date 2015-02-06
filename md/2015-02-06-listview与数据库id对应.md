&emsp;&emsp;很快，问题出现了，因为要使用自己定义xml文件的listview，所以必须使用SimpleAdapter，本来写出来感觉没有问题，列表项目创建和删除都正常。突然试着试着就发现有项目不能删除了。![删除该项](http://i.imgur.com/gHXz5N9.png)

查看了数据库后发现这个列表项的id值为6，但是在listview里面id是1，所以删不掉了。仔细想想才意识到问题在于数据库的id是自动添加，删除了前面的数据，但是后面新添加的数据id是从上一条记录往上加1的，这也就造成了这个问题。

&emsp;&emsp;解决方法呢，当时也想了挺多的，最为有效并且无伤的应该是创建一个HashMap保存listview中item的id和数据库中id的对应关系，这么一想还是挺好实现的。

在DAO里面添加这样一段：
		
		public HashMap<Integer,Integer> getIds(){
		        HashMap<Integer,Integer> ids = new HashMap<Integer,Integer>();
		        Integer i = 0;
		        SQLiteDatabase db = helper.getReadableDatabase();
		        Cursor cursor = db.rawQuery("select id from event", null);
		        while (cursor.moveToNext()) {
		            Integer l = cursor.getInt(0);
		            ids.put(i, l);
		            i++;
		        }
		        return ids;
		    }
从数据库里取到的id值依次在Map里与0，1,2,3...对应。

下面只需要在创建adapter的时候也创建好这个HashMap，在删除操作的时候只需要将原来的改成

`eventDAO.delete(ids.get(listItemId));`

就ok了。


> 又一次在实践中学到了经验。