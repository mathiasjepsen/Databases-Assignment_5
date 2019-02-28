# Databases-Assignment_5
## Exercise 1
```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `denormalizeComments`(IN p_PostId INT)
BEGIN
    UPDATE posts SET Comments = (
	SELECT JSON_ARRAYAGG(postComment) AS postComments
	FROM (SELECT Id FROM posts) AS subPosts
	INNER JOIN (SELECT PostId, JSON_OBJECT("Score", Score, "Text", Text, "CreationDate", CreationDate, "UserId", UserId) AS postComment FROM comments WHERE PostId = p_PostId) AS postCommentsTable ON postCommentsTable.PostId = p_PostId
	WHERE subPosts.Id = p_PostId
    )
    WHERE posts.Id = p_PostId;
END
```
---
## Exercise 2
```sql
CREATE DEFINER=`root`@`localhost` TRIGGER `comments_AFTER_INSERT` AFTER INSERT ON `comments` FOR EACH ROW 
BEGIN
    CALL denormalizeComments(NEW.PostId);
END
```
---
## Exercise 3
```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `addNewComment`(IN p_Id INT, IN p_PostId INT, IN p_Score INT, IN p_Text TEXT, IN p_CreationDate DATETIME, IN p_UserID INT)
BEGIN
    INSERT INTO comments VALUES (p_Id, p_PostId, p_Score, p_Text, p_CreationDate, p_UserId);
    CALL denormalizeComments(p_PostId);
END
```
